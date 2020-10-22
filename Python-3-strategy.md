Many of the mainstream Linux operating systems are beginning to ship with only Python 3 installed.
Python 2 must be installed explicitly and, eventually, will no longer be supported. And so, we must
eventually transition to Python 3. This guide outlines how.

# Goal

Our primary goals for WeeWX Version 4 (to be released 2019):

* To allow WeeWX to run under Python 2.7, or Python 3.5 or greater.
* Best we can, allow old, unmodified drivers, services, and uploaders to run under Python 2 and
WeeWX Version 4. They cannot be expected to run under Python 3.

Why these versions?

- Python 2.6 was last released in 2008-10-1, well over 10 years ago. It is missing some features that are used in WeeWX, particularly in the test suites. It is no longer supported by the Python maintainers.

- Python 3.5 includes explicit Unicode literals ([PEP 414](https://www.python.org/dev/peps/pep-0414/)), as well as the use of the `%` operator with byte-strings, making it much more backwards compatible with Python 2.7.

# Strategy

Our strategy is to support both Python 2 and 3, at least for a while. This guide covers how to convert and run
code to do that. It starts with the easy stuff. There is a [separate section](https://github.com/weewx/weewx/wiki/Python-3-strategy#strings) that discusses strings.

The tool [`python-modernize`](https://python-modernize.readthedocs.io/en/latest/) is very useful. It
will scan your code, and update it for Python 2 and 3 compatibility, using the library
[`six`](https://six.readthedocs.io/). If used with the `-w` ("write") flag, it will write out the changes. It will take care of many little details such as converting `print` statements to the Python 3 function style, rewriting iterators that use `.iteritems()`, etc.

The rest of this guide assumes you have used `python-modernize`, and now you need to review the results.


# Strings
Most of your time will be spent thinking about strings. They are treated very differently between
Python 2 and 3. Indeed, this was the major motivation in creating Python 3, with all its upwards
incompatibilities. 

Make sure you thoroughly understand the difference between byte sequences and Unicode. This is a classic
article on the subject: [The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)

In Python 2, `str` and `unicode` are separate types. An `str` is a sequence of bytes (also called "byte-string"), a `unicode` is a sequence of code points. You go back and forth between them by using `encode` and `decode`.

In Python 3, there is no type `unicode`, only `str`, which like Python 2's `unicode`, is a sequence of code points. By default, all strings are Unicode. Python 3 has a separate type, `bytearray` or `bytes`, to represent sequences of bytes. Like Python 2, you go back and forth between them using `encode` and `decode`, except the byte representation ends up as `bytes`, not `str`.

There are two general strategies we could use:

1. Use the native string type under each version of Python. This would be the byte-string under Python 2, and the Unicode string under Python 3. Convert as appropriate. This means all routines that need to do a conversion, typically I/O, must be aware of which version of Python they're running under, or use a library that can do the determination. It's worth noting that this strategy (of using the native string type) is basically what we do with WeeWX V3.x, where the native type under Python 2 is a byte-string. Unicode strings are only rarely used internally.

2. Use Unicode throughout WeeWX for both versions of Python. With this strategy, on input, we must always convert byte-strings into Unicode, then, on output, convert them back. This approach has the advantage that internally, strings are always in Unicode, so it is not necessary to know which version of Python you are running under. 

Conclusion? We have chosen **strategy #2**. While it means rewriting more code, in the end, it will result in fewer surprises and will be more future proof.


## Caveats
While strategy 2 results in simpler, cleaner code, it does have a few things you need to watch out for. Consider this comparison:

```python
   if record_generation == "software":
```
Under Python 3, both sides of the equality test are in Unicode, so there is no problem. However, using strategy #2, under Python 2, the variable `record_generation` will be a Unicode string, while the literal `"software"` is a byte-string. We're comparing apples to oranges. Fortunately, Python 2 will silently convert ("decode") the right hand side, using the `ascii` codec, into a Unicode string, so now it is comparing two Unicode sequences. 

This works fine, except when the right hand side cannot be decoded using the `ascii` codec. Consider this:

```python
if unit_label == '°C':
```
In this example, we are comparing a Unicode string on the left, and something that cannot be converted into Unicode using the `ascii` codec on the right. So, under Python 2, the result will not only always be `False`, it will be silently `False` --- no exception will be raised.

The conclusion is that if there is any chance that one or the other side of a comparison is a byte-string,
which cannot be converted into Unicode using the `ascii` codec, we must do the conversion ourselves. The fix for this example is easy:

```python
if unit_label == u'°C':
```

This will work under both Python 2 and Python 3.

Other cases may not be so simple. Fortunately, in WeeWX, we can pretty much count on all comparisons as being representable in ASCII.


## I/O

There are four areas that must convert byte strings to and from Unicode:

1. Device drivers
2. ConfigObj
3. Templates
4. RESTful services

### Device drivers

Carefully review the semantics of any strings in the file and decide whether it's a true string (which will be Unicode under strategy #2), or a sequence of bytes. Most of the time, you want a true string, but an important exception is strings used in drivers sent to and from the hardware. These are really sequences of bytes.

Here's an example. This is a function from the Vantage driver, which turns the console lamp on and off:

```python
def setLamp(self, onoff='OFF'):
  """Set the lamp on or off"""
  try:        
    _setting = {'off': '0', 'on': '1'}[onoff.lower()]
  except KeyError:
    raise ValueError("Unknown lamp setting '%s'" % onoff)

  _command = "LAMPS %s\n" % _setting
  self.port.send_command(_command, max_tries=self.max_tries)

   syslog.syslog(syslog.LOG_NOTICE, "vantage: Lamp set to '%s'" % onoff)
```       
Most of the strings in the function are true strings and can be left unchanged. However, the command sent
to the hardware, `"LAMPS %s\n"`, will be represented as a Unicode string under Python 3. We want a sequence of one-byte characters, not a Unicode string. The function becomes:


```python
def setLamp(self, onoff='OFF'):
  """Set the lamp on or off"""
  try:        
    _setting = {'off': b'0', 'on': b'1'}[onoff.lower()]
  except KeyError:
    raise ValueError("Unknown lamp setting '%s'" % onoff)

  _command = b"LAMPS %s\n" % _setting
  self.port.send_command(_command, max_tries=self.max_tries)

   syslog.syslog(syslog.LOG_NOTICE, "vantage: Lamp set to '%s'" % onoff)
```       

We have changed two lines of code. The line

```python
    _setting = {'off': '0', 'on': '1'}[onoff.lower()]
```

became

```python
    _setting = {'off': b'0', 'on': b'1'}[onoff.lower()]
```

and the line
```python
  _command = "LAMPS %s\n" % _setting
```

became

```python
  _command = b"LAMPS %s\n" % _setting
```

Consider this latter change. Under Python 2, there will be no change --- a byte string (prefix `b`) and a
regular string are aliases of each other. But, under Python 3, without the prefix, the string 
would be a sequence of Unicode characters --- not what we want. Instead, by 
adding the `b` prefix, it becomes, like in Python 2, a sequence of one-byte characters, type `bytes`.
So, the resultant code works with either version.

#### Integers and strings
Here's another problem. Say you need to send a byte value of `10` to the device as binary. Under Python 2, you could do this

```python
x = chr(10)
port.write(x)
```

The function `chr()` converts its argument into a string, which in the case of Python 2, is a sequence of one-byte characters. A string only one element long, (`'\n'`) is created. Problem solved. 

But, under Python 3, what is actually created is a Unicode string --- not at all what you want. So, you must explicitly tell it to create a sequence of one-byte characters (type `bytes`):

```python
import struct
...
x = struct.pack('>b', 10)
port.write(x)
```

Fortunately, this solution, while wordy, will work under both Python 2 and 3. 

The library six provides a function, `int2byte()` that does precisely this. It will work under both Python 2 and 3.

```python
import six
x = six.int2byte(10)
port.write(x)
```

### ConfigObj
By default, under Python 2, `ConfigObj` will not convert byte-strings into Unicode on input. They remain as byte strings. If we are to adopt strategy #2 above, we must tell `ConfigObj` to convert the byte-strings to Unicode on input, then convert them back on output. Option [`encoding`](https://configobj.readthedocs.io/en/latest/configobj.html#encoding) does this. Here's an example:

```python
>>> c = configobj.ConfigObj('/home/weewx/skins/Standard/skin.conf', encoding='utf8')
>>> degree_C_label = c['Units']['Labels']['degree_C']
>>> print(degree_C_label)
°C
>>> print(type(degree_C_label))    # Python 2
<type 'unicode'>
```

Under Python 3, for this last result, you get

```python
>>> print(type(degree_C_label))    # Python 3
<type 'str'>
```

which is also Unicode.

*Cavaet*: you must be careful not to write a `ConfigObj` object that contains non-ASCII byte-strings to a file. If you do, then when you try to write the object, `ConfigObj` will first convert the byte-string to Unicode, then convert back, then write. If the byte-string is not convertible via an ASCII codec, you will get an error. The solution is to either specify option [`default_encoding`](https://configobj.readthedocs.io/en/latest/configobj.html#default-encoding) in the constructor, or, even better, make sure you put only Unicode strings in your `ConfigObj`.

### Templates
Cheetah V2 will not run under Python 3. However, the newer version, Cheetah V3, will run under both Python 2 and 3. Unfortunately, it only supports Python 2.7, so we may have to drop 2.6 support.

The `respond()` method of a compiled template is responsible for filling in the placeholders from the search list. For both Cheetah V2 and V3, it returns Unicode. Example:

```python
# coding=utf-8
import Cheetah.Template

import weewx.cheetahgenerator

searchList = {'int': 6,                 # A primitive 
              'byte_': b"\xc2\xb0C",    # A byte string, under both Python 2 & 3
              'unicode_': u"°C",        # Unicode under both Python 2 & 3
              'str_': "°C"}             # Version dependent. Byte string under Python 2, unicode under Py3

c = Cheetah.Template.Template(source="""<p>int=$int; byte=$byte_; unicode=$unicode_; str=$str_</p>""",
                              searchList=searchList,
                              filter='assure_unicode',
                              filtersLib=weewx.cheetahgenerator
                              )

x = c.respond()
print("type(x)=%s" % type(x))

s = x.encode('ascii', 'xmlcharrefreplace')

print(s)
```

This uses a filter that guarantees that any placeholders result in Unicode. It looks like this:

```python
class assure_unicode(Cheetah.Filters.Filter):
    """Assures that whatever a search list extension might return, it will be converted into
    Unicode. """

    def filter(self, val, **dummy_kw):
        if val is None:
            filtered = u''
        elif isinstance(val, six.text_type):
            # It's already Unicode. Just return it.
            filtered = val
        elif isinstance(val, six.binary_type):
            # It's a byte-sequence. Decode into Unicode.
            filtered = val.decode('utf-8')
        else:
            # It's not Unicode and it's not a byte-string. Must be a primitive.
            #   Under Python 2, six.text_type is equivalent to calling unicode(val).
            #   Under Python 3, it is equivalent to calling str(val).
            # So, the results always end up as Unicode.
            filtered = six.text_type(val)
        return filtered
```

Results of running the program under Python 2:
```
type(x)=<type 'unicode'>
<p>int=6; byte=&#176;C; unicode=&#176;C; str=&#176;C</p>
```
Results under Python 3:
```
type(x)=<class 'str'>
b'<p>int=6; byte=&#176;C; unicode=&#176;C; str=&#176;C</p>'
```

which is exactly what we want. The Cheetah generator in WeeWX uses a strategy similar to the above, except it also handles unidentified objects that cannot be 
evaluated.

#### Search list extensions
Note how Cheetahs's `respond()` method returns Unicode. What about search list extensions (SLE)? The Cheetah engine embeds any expressions
they create in the Unicode string, then concatenate everything together. This means whatever the SLE returns must be convertible into
Unicode. To guarantee this, follow a simple rule: __All search list extensions should return Unicode__

If you follow this rule, you will not have any conversion issues.

# Tools
## `six`
The examples above make use of the utility [`six`](https://six.readthedocs.io/). It rationalizes away the differences between Python 2 and 3, making it easier to write portable code. It will ship with WeeWX
Version 4, so its presence can be counted on in that context. 

However, if you are an extension writer, and you want your extension to work under older versions of WeeWX,
as well as V4, then you cannot count on it being around. You will have to write portable code without its
help.

## `pyenv`
If you are a developer, you will find yourself switching back and forth between Python 2 and Python 3. Each
will need its own pre-requisites. It can be hard to keep track of the separate environments. I have found
the tool [`pyenv`](https://github.com/pyenv/pyenv) invaluable to do this.


# Packaging

Although python2 is now past end-of-life, there are still plenty of good reasons to keep using it, and weewx4 will continue to work with both python2 and python3 for awhile.

How do we minimize the number of packages?  Given the different levels of python2 and python3 support in each operating system, and the many different ways of installing python (2 or 3) on any platform, how do we minimize the number of packages that provide maximum platform options?

* Provide python2 and python3 packages for each major linux distribution (debian, redhat, suse)
* Use 'pure python' approach (setup.py) for all systems

This results in 7 packages that must be distributed.  In fact, there are only really 4 definitions that must be maintained, since the python2/python3 variants for debian/redhat/suse are identical, differing only in the interpreter invocation and the dependencies.

## Dependencies

Although the dependencies for weewx are minimal, they are packaged and named on each operating system in many different ways.  We can use the dependency mechanism on each platform to hide that complexity from users who use the debian/redhat/suse packages, but we must enumerate each of the variations for those who use the setup.py approach, at least if they want the system-provided pre-requisites.  The pure python approach (use pip to install pre-requisites) does not really simplify the problem, since some of the pre-requisites (e.g., python-imaging) still have lower-level non-python requirements that are specific to each platform.

## The shebang

The entry points for the weewx code will continue to use this pattern for the shebang:
```
#!/usr/bin/env python
```
When install is done using setup.py, the shebang will be replaced with a tight binding to whichever python was invoked to do the install.  This is true whether done directly or within a virtual environment.

When running weewx directly from a repository clone, the `python` binding will be lazy.  This provides maximum flexibility for development and quick testing.

When running from per-platform package, the shebang will remain as `/usr/bin/env python`, but the python interpreter will be specified in `/etc/default/weewx`.  The actual entry points are shell stubs that are placed in `/usr/bin`, so they are automatically in the user's path.  Those stubs invoke the interpreter specified in `/etc/default/weewx`.  As a result, the python2 and python3 installations differ only in the contents of the `/etc/default/weewx` file (and of course any compiled code in `*.pyc` or `__pycache__`).

The baseline file `/etc/default/weewx` looks like this:
```
WEEWX_PYTHON=python3
WEEWX_BINDIR=/home/weewx/bin
WEEWX_BIN=/home/weewx/bin/weewxd
WEEWX_CFG=/home/weewx/weewx.conf
```
It is used by the entry points in `/usr/bin/wee*` and the init script `/etc/init.d/weewx`

## Running as a daemon

The initial release of weewx4 still uses `init.d` on all platforms except suse.  At some point we may convert the rpm and deb packages to use a systemd unit file, but not until:

* we can be sure that there is only one unit file that will work on all platforms
* the unit file will use the `/etc/default/weewx` contents
* there is a standard pattern to use in the weewx documentation that is not specific to systemd

There are still platforms that do not use systemd, and never will.


# Resources
[The Conservative Python 3 Porting Guide](https://portingguide.readthedocs.io/en/latest/index.html)<br/>
"This is an opinionated guide. It explains one tried way to do the porting, rather than listing all alternatives and leaving you to research them and choose."

[Six: Python 2 and 3 Compatibility Library](https://six.readthedocs.io/)<br/>
"Six provides simple utilities for wrapping over differences between Python 2 and Python 3. It is intended to support codebases that work on both Python 2 and 3 without modification. six consists of only one Python file..."

[Python-Modernize](https://python-modernize.readthedocs.io/en/latest/)<br/>
A superior alternative to `2to3`
