Many of the mainstream Linux operating systems are beginning to ship with only Python 3 installed.
Python 2 must be installed explicitly and, eventually, will no longer be supported. And so, we must
be ready for Python 3.

Our strategy is to support both, at least for a while. This guide covers how to convert and run
code that will run under both Python 2 and Python 3.

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

5. Carefully review the semantics of any strings in the file and decide whether its a true string (which will become Unicode under Python 3), or what I'll call a "byte string." This is what will take most of your effort. Most of the time, you want a true string, but an important exception is strings used in drivers sent to and from the hardware. These are really sequences of bytes.

   This matters because in Python 3, strings and byte strings are cleanly separated, while in Python 2, a string serves both purposes. To write code that will run under both versions of Python, you must be clear which role you intend the string to play.

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
each other. But, under Python 3, the string would be a sequence of Unicode characters. Instead, it becomes,
like in Python 2, a sequence of one-byte characters.

6. Carefully review any iterators, in particular the `range` and `zip` functions. For example, under Python 2,

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

