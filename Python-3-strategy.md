Many of the mainstream Linux operating systems are beginning to ship with only Python 3 installed.
Python 2 must be installed explicitly and, eventually, will no longer be supported. And so, we must
eventually transition to Python 3. This guide outlines how.

# Goal

Our primary goals for WeeWX Version 4 (to be released 2019):

* To allow WeeWX to run under Python 2.6, Python 2.7, or Python 3.3 or greater.
* Best we can, allow old, unmodified drivers, services, and uploaders to run under Python 2 and
WeeWX Version 4. They cannot be expected to run under Python 3.

Why these versions?

Python 2.5 was last released in 2011-05-26, well over 7 years ago. It is missing many key
features, which would make an upgrade to Python 3 difficult. 
In particular, "print as a function (PEP 3105)," and the new exception catching format (PEP 3110) are missing.

And so, we limit our Python 2 support to Python 2.6 and Python 2.7.

Python 3.3 introduced explicit Unicode literals ([PEP 414](https://www.python.org/dev/peps/pep-0414/)),
making it much more backwards compatible with Python 2.7.

# Strategy

Our strategy is to support both Python 2 and 3, at least for a while. This guide covers how to convert and run
code to do that. It starts with the easy stuff. There is a [separate section](https://github.com/weewx/weewx/wiki/Python-3-strategy#strings) that discusses strings.

The tool [`2to3`](https://docs.python.org/2/library/2to3.html) is useful, but not definitive. 
The problem is that it is designed for a one-way conversion from Python 2 to 3, not to support 
both versions. If used with the `-w` ("write") flag, it will make changes that will break compatibility with Python 2.  Use it to find incompatibilities, not to fix them

1. Use the utility `2to3` with the `-l` flag to see what needs to be changed. Note that this tool will
not investigate any doctest code embedded in comments, so you'll still have to scan the file manually.

2. Change any print statements to the "function style" Python 3 print statement. For example,

       print a, b, c

   becomes

       print(a,b,c)

   Under Python 2, this will be interpreted as printing a tuple `(a,b,c)`, so to finish the job, you must add a "future" statement to the top of the file, so it will use Python 3 semantics:

       from __future__ import print_function

3. All raised exceptions must use the constructor style. Do this:

   ```python
     raise ValueError("Bad value: %s" % v)
   ```
   not this:
   ```python
     raise ValueError, "Bad value: %s" % v
   ```

4. Similarly, all "caught" exceptions must use the `as` style. Do this

   ```python
   except ValueError as e:
   ```

   not this

   ```python
     except ValueError, e
   ```
5. Carefully review any iterators, in particular the `range` and `zip` functions. For example, under Python 2,

   ```python
   zip(['a', 'b'], [1, 2])
   ```

   results in a list

   ```
   [('a', 1), ('b', 2)]
   ```

   but under Python 3, it results in an iterator object. You must explicitly convert it into a list:

   ```python
   list(zip(['a', 'b'], [1, 2]))
   ```
   This will work under both Python 2 and 3. 

   The `range` operator has a similar problem.

6. Many things were renamed in Python 3, while other things have moved. To facilitate both Python 2 and 3, we
use the compatibility library [six](https://six.readthedocs.io/). 

   For example, the class `StringIO`, formerly found in the module `StringIO` but now found in module `io`, 
can be easily imported in both versions using

   ```python
   from six.moves import StringIO
   ```

   In Python 2, you use the function `raw_input()` to read from the console. In Python 3, the function
name has been changed to `input()`, which does not exist under Python 2, and `raw_input()` has been
eliminated. So, neither `input` nor `raw_input` will work under both versions. The library `six` provides
a solution

   ```python
   from six.moves import input
   answer = input("y or n")
   ```

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

2. Use Unicode with both versions of Python. With this strategy, on input, we must always convert byte-strings into Unicode, then, on output, convert them back. This approach has the advantage that internally, strings are always in Unicode, so it is not necessary to know which version of Python you are running under. Still, there are issues. Consider this code:

   ```python
      if record_generation == "software":
   ```
   Under Python 3, both sides of the equality test are in Unicode, so there is no problem. However, using strategy #2, under Python 2, the variable `record_generation` will be a Unicode string, while the literal `"software"` is a byte-string. We're comparing apples to oranges. Fortunately, Python 2 will silently convert ("decode") the right hand side, using an ASCII codec, into a Unicode string, so now it is comparing two Unicode sequences. 

   This works fine, except when the right hand side cannot be decoded using an ASCII codec. Consider this:

   ```python
   if unit_label == '°C':
   ```
   In this example, we are comparing a Unicode string on the left, and something that cannot be converted into Unicode using the ASCII codec on the right. So, under Python 2, the result will always be `False`. 

   The conclusion is that if there is any chance that one or the other side of a comparison is a byte-string,
which cannot be converted into Unicode using the ASCII codec, we must do the conversion ourselves. The fix for this example is easy:
 
   ```python
   if unit_label == u'°C':
   ```

   Other cases may not be so simple. Fortunately, in WeeWX, we can pretty much count on all comparisons as being representable in ASCII.

Conclusion? I think it best to use **strategy #2**. While it means rewriting more code, in the end, it will result in fewer surprises and will be more future proof.

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
to the hardware, `"LAMPS %s\n"`, will be represented as a Unicode string under Python 3. We want a sequence of one-byte characters, not a Unicode string. So, the relevant line becomes

```python
  _command = b"LAMPS %s\n" % _setting
```

Under Python 2, there will be no change --- a byte string (prefix `b`) and a regular string are aliases of
each other. But, under Python 3, without the prefix, the string would be a sequence of Unicode characters --- not what we want. Instead, by adding the `b` prefix, it becomes, like in Python 2, a sequence of one-byte characters, type `bytes`. So, the resultant code works with either version.

### Integers and strings
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

Cavaet: you must be careful not to write a `ConfigObj` object that contains non-ASCII byte-strings to a file. If you do, then when you try to write the object, `ConfigObj` will first convert the byte-string to Unicode, then convert back, then write. If the byte-string is not convertible via an ASCII codec, you will get an error. The solution is to either specify option [`default_encoding`](https://configobj.readthedocs.io/en/latest/configobj.html#default-encoding) in the constructor, or, even better, make sure you put only Unicode strings in your `ConfigObj`.

### Templates
Cheetah V2 will not run under Python 3. However, the newer version, V3, will run under both Python 2 and 3. Unfortunately, it only supports Python 2.7, so we may have to drop 2.7 support.

When Cheetah V2 reads the template file, it uses a simple open and read, so the results end up as a byte-string, most likely UTF-8 encoded.

By contrast, Cheetah V3 uses a codec to read the file, resulting in Unicode. So, it always traffics in Unicode, which is what we want.

The search lists should probably also provide Unicode, although the filters we use can probably convert if 
the placeholders are returned in something else.


### RESTful services
to be done

# Installing Python 3 prerequisites
On Debian:

    sudo apt install python3-serial
