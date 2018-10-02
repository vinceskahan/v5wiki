Many of the mainstream Linux operating systems are beginning to ship with only Python 3 installed.
Python 2 must be installed explicitly and, eventually, will no longer be supported. And so, we must
be ready for Python 3.

Our strategy is to support both, at least for a while. This guide covers how to convert and run
code that will run under both Python 2 and Python 3. It starts with the easy stuff. There is a separate
section on the end that deals with strings.

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

6. In Python 2, you use the function `raw_input()` to read from the console. In Python 3, the function
name has been changed to `input()`, which does not exist under Python 2, and `raw_input()` has been
eliminated. So, neither `input` nor `raw_input` will work under both versions.

   WeeWX provides a function, `weeutil.weeutil.input`, which will work under either version. So, convert all
calls to `raw_input` to use it:

   ```python
     ans = weeutil.weeutil.input("Answer 'yes' or 'no')
   ```

## Strings
Most of your time will be spent thinking about strings. They are treated very differently between
Python 2 and 3. Indeed, this was the major motivation in creating Python 3, with all its upwards
incompatibilities. 

Make sure you thoroughly understand the difference between byte sequences and Unicode. This is a classic
article on the subject: [The Absolute Minimum Every Software Developer Absolutely, Positively Must Know About Unicode and Character Sets (No Excuses!)](https://www.joelonsoftware.com/2003/10/08/the-absolute-minimum-every-software-developer-absolutely-positively-must-know-about-unicode-and-character-sets-no-excuses/)

In Python 2, `str` and `unicode` are separate types. An `str` is a sequence of bytes, a `unicode` is a sequence of code points. You go back and forth between them by using `encode` and `decode`.

In Python 3, there is no type `unicode`, only `str`, which like Python 2's `unicode`, is a sequence of code points. By default, all strings are Unicode. Python 3 has a separate type, `bytearray` or `bytes`, to represent sequences of bytes. Like Python 2, you go back and forth between them using `encode` and `decode`, except the byte representation ends up as `bytes`, not `str`.

There are two general strategies we could use:

1. Use the native string type under each version of Python. This would be the byte-sequence string under Python 2, and the Unicode string under Python 3. Convert as appropriate. This means all routines that need to do a conversion, typically I/O, must be aware of which version of Python it's running under, or use a library that can do the determination. Note that this strategy of using the native string type is basically what we do with WeeWX V3.x, where the native type under Python is a byte string.

2. Use Unicode in both versions. With this strategy, we must always convert byte sequences on input into Unicode, then convert back when it comes type to do output. This is the way most software deals with Unicode. This approach has the advantage that internally, strings are always in Unicode. It's not necessary to know which version of Python you're running under.  Note that with this strategy, it is not necessary to annotate every literal string as unicode by using the `u` prefix. Most conversions will happen automatically. For example, consider something like this:

   ```python
      if record_generation == "software":
   ```
   Under Python 3, all strings are Unicode, so there is no problem. However, using strategy #2, under Python 2, the variable `record_generation` will be a Unicode string, while the literal `"software"` is a byte string. We're comparing apples to oranges. What happens is that Python 2 will silently convert ("decode") the right hand side, using an ASCII codec, into a Unicode string, so it is comparing two Unicode sequences. 

   This works fine, except when the right hand side cannot be decoded using an ASCII codec. Consider this:

   ```python
   a=u'ą'
   a=='ą'  # False!
   ```
   In this example, we are comparing a Unicode string on the left, and something that cannot be converted into Unicode using the ASCII codec on the right. So, the result is `False`
 
   The correct, most general way to do this is as follows:

   ```python
      if record_generation.decode('utf8') == "software":
   ```

   Fortunately, in WeeWX, we can pretty much count on all comparisons as being representable in ASCII.

### I/O

There are four areas that must perform this I/O:

1. Device drivers
2. ConfigObj
3. Templates
4. RESTful services

### Device drivers

Carefully review the semantics of any strings in the file and decide whether it's a true string (which will be Unicode under Python 3), or what I'll call a "byte string." Most of the time, you want a true string, but an important exception is strings used in drivers sent to and from the hardware. These are really sequences of bytes.

To write code that will run under both versions of Python, you must be clear which role you intend the string to play.

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
to the hardware, `"LAMPS %s\n"`, is actually a byte string. We want a sequence of one-byte characters, not a Unicode string. So, the relevant line becomes

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

WeeWX provides a function, `weeutil.weeutil.int2byte()` that does precisely this. It will work under both Python 2 and 3.

## ConfigObj
To be done

## Installing Python 3 prerequisites
To be done