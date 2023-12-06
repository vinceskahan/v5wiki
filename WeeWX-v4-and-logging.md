# Overview

WeeWX V3 uses Python's `syslog` module to log events. It has
several disadvantages:
- It works only under versions of *nix. No Windows.
- It can log only to the system log.
- It is inflexible in the formatting it uses.

WeeWX V4 uses Python's
[`logging`](https://docs.python.org/3/library/logging.html) package. It has
several advantages:
- It abstracts out logging destinations, formatting, and filtering, allowing all
  to be changed at runtime.
- It has destinations that work on Windows.
- It can support logging to email, sockets, and rotating files.

In short, it is a much more flexible facility. Plus, it's easy to use.

Both the `syslog` module and the `logging` module can send log
messages to the system's logger.  In fact, that is the default
behavior for WeeWX.  WeeWX sends log messages to the system logger
so that the system will handle log file rotation, filtering of
log messages, sending messages to remote log servers, etc.

This is a guide to how `logging` is implemented within WeeWX.

# Configuring logging
The big advantage of the `logging` module is its ability to be extensively
customized. By default, WeeWX makes sensible choices, but you may want to change
them. This section is about how to do this.

First, read, or, at least, attempt to read, the section on the [schema of the
configuration dictionary](https://docs.python.org/3/library/logging.html#module-logging) 
that `logging` uses. It's dense and hard to follow, but it will eventually sink 
in.

## Defaults
With that in mind, here is the default configuration that WeeWX uses:

```ini
[Logging]
    version = 1
    disable_existing_loggers = False

    # Root logger
    [[root]]
      level = {log_level}
      handlers = syslog,

    # Additional loggers would go in the following section. This is useful for tailoring logging
    # for individual modules.
    [[loggers]]

    # Definitions of possible logging destinations
    [[handlers]]

        # System logger
        [[[syslog]]]
            level = DEBUG
            formatter = standard
            class = logging.handlers.SysLogHandler
            address = {address}
            facility = {facility}

        # Log to console
        [[[console]]]
            level = DEBUG
            formatter = verbose
            class = logging.StreamHandler
            # Alternate choice is 'ext://sys.stderr'
            stream = ext://sys.stdout

    # How to format log messages
    [[formatters]]
        [[[simple]]]
            format = "%(levelname)s %(message)s"
        [[[standard]]]
            format = "{process_name}[%(process)d] %(levelname)s %(name)s: %(message)s" 
        [[[verbose]]]
            format = "%(asctime)s  {process_name}[%(process)d] %(levelname)s %(name)s: %(message)s"
            # Format to use for dates and times:
            datefmt = %Y-%m-%d %H:%M:%S
```

This configures three different facilities:
1. Loggers, which expose the interface that application code uses directly. Most
   importantly, it determines which *handler(s)* to use. Note that the `root`
   logger is in its own section.
2. Handlers, which send the log records (created by loggers) to an appropriate
   destination. There are two handlers that are included in the default
   configuration: `syslog` and `console`.
3. Formatters, which specify the layout of log records in the final output. A
   number of attributes are available to the formatter (such as `%(levelname)s`; 
   see the documentation [*LogRecord attributes*](https://docs.python.org/3/library/logging.html#logrecord-attributes) 
   for a complete list).

The value for `{log_level}` depends on whether the `debug` option has
been set. If it has not (the default), then `log_level` is `INFO`, otherwise,
`DEBUG`.

The value for `{process_name}` is passed in when setting up the logging
facility. For the WeeWX main program, it is `weewxd`.

## Logging to rotating files

While WeeWX comes with two handlers (`syslog` and `console`) there are 
many others. See the Python documentation 
[*Logging handlers*](https://docs.python.org/3/library/logging.handlers.html).

One handler that is particularly useful, especially on **macOS**, is the
[`TimedRotatingFileHandler`](https://docs.python.org/3/library/logging.handlers.html#timedrotatingfilehandler),
which writes to a specified file. At a specified time, a new
file is opened and 'rotated' into place. Here's how to specify and use it.

Simply add this to your `weewx.conf` file:
```ini
[Logging]

    # Root logger
    [[root]]
      handlers = timed_rotate,                                  # NOTE 1

    [[handlers]]
        # Log to a set of rotating files
        [[[timed_rotate]]]                                      # NOTE 2
            level = DEBUG
            formatter = verbose                                 # NOTE 3
            class = logging.handlers.TimedRotatingFileHandler   # NOTE 4
            # File to log to:
            filename = /var/tmp/weewx.log                       # NOTE 5
            # When to rotate:
            when = midnight                                     # NOTE 6
            # How many log files to save
            backupCount = 7                                     # NOTE 7
```

1. This reconfigures the `root` logger to send log messages to the hander 
   `timed_rotate`, instead of to the default `syslog`. The trailing comma is
   important: it tells Python that option `handlers` is a list, and not a single
   item. Because it is a list, you can actually specify multiple handlers. For
   example, to log to `timed_rotate` as well as the `console`, the option becomes:

        handlers = timed_rotate, console
 
2. This is where we define the new handler. It will be named `timed_rotate`.

3. The `verbose` formatter is useful when doing file rotations because it
   includes a timestamp.

4. Handler `timed_rotate` will use class
   [`TimedRotatingFileHandler`](https://docs.python.org/3/library/logging.handlers.html#timedrotatingfilehandler)
   for the actual implementation. 

5. It will log to the file `/var/tmp/weewx.log`. In WeeWX V5, this location can
   be a relative path, in which case it will be relative to `WEEWX_ROOT`. For
   example, specifying

       filename = log/weewx.log
 
   in Version 5 will cause the log to go to `~/weewx-data/log/weewx.log`. 

7. Do the rotation at midnight.

8. Save seven (7) backup files, that is, a week's worth.


## Customizing what gets logged

Another example. When the `debug` option is on, the default `root` logger will
show `DEBUG` messages and above &mdash; basically everything. This is fine, but
say you've decided that the `weewx.restx` module is too chatty for your liking.
For it, you want to see only `INFO` messages and above &mdash; nothing else.

The solution is to tailor the logger used by the `weewx.restx` module, so it no
longer inherits properties from the root logger. For this logger, we will
specify that it logs only `INFO` and above. To do this, the `[Logging]` section
in `weewx.conf` would look like:

```ini
[Logging]
  [[loggers]]
    [[[weewx.restx]]]
      level = INFO
```

Another example. Suppose you are debugging the `weewx.restx` module. In this
case it would be desirable to set its logging `level` to `DEBUG` while other
loggers continue to log `INFO` or higher. Again, the solution is to customize
the logger for the `weewx.restx` module.  In this case, along with setting the
logging `level`, we need to define a `handler`. Because the `weewx.restx` logger
will handle the logging for the `weewx.restx` module, we will also want to turn
`propagate` off. Otherwise `INFO` and higher messages will be logged twice: once
by the `weewx.restx` logger and also by any parent handlers. Lastly, `debug`
should be left at 0, so that the default `root` logging is `INFO`.

The configuration would look something like this.

```ini
debug = 0

.
.
.

[Logging]
  [[loggers]]
    [[[weewx.restx]]]
      level = DEBUG
      handlers = syslog,
      propagate = 0
```

## Disabling logging

This example essentially disables WeeWX logging completely, resulting in no log
messages at all related to WeeWX other than those perhaps written by the
operating system itself.

```
[Logging]
    version = 1
    disable_existing_loggers = True

    # Root logger
    [[root]]
      level = CRITICAL
```


# For developers

What follows is intended for developers who are extending WeeWX.

## In modules

It's very easy to use `logging` from within a module, such as a new service,
search list extension, etc. At the top of your code, include

```python
import logging

log = logging.getLogger(__name__)
```

Then whenever you want to log an event, simply call one of

```python
log.debug("This is a debug message")
log.info("This is an informational message")
log.warning("This is a warning message")
log.error("This is an error message")
log.critical("This is a critical message")
```
That's it.


## Throttling logging events

The Python `logging` module makes it very easy to temporarily reduce the number
of uninteresting messages going into a log.

Say you're about to call a function which will insert hundreds of records into
the database using the `addRecord()` method of the `Manager` class. This method
logs an `INFO` event for every insertion, resulting in hundreds of
not-terribly-interesting entries. You'd like to temporarily avoid this. Here's
how:

```python
import logging

# Temporarily disable logging for events at or below INFO
logging.disable(logging.INFO)

generate_zillions_of_records()

# Remove the temporary restriction
logging.disable(logging.NOTSET)
```

See the Python docs for more details about
[`logging.disable()`](https://docs.python.org/3/library/logging.html#logging.disable).

## Maintaining backwards compatibility

If you are an extension writer, you will want to support not only the new style
logging, but also the old `syslog` style. Here's how you can do it.

```python
try:
    # Test for new-style weewx logging by trying to import weeutil.logger
    import weeutil.logger
    import logging
    log = logging.getLogger(__name__)

    def logdbg(msg):
        log.debug(msg)

    def loginf(msg):
        log.info(msg)

    def logerr(msg):
        log.error(msg)

except ImportError:
    # Old-style weewx logging
    import syslog

    def logmsg(level, msg):
        # Replace '__name__' with something to identify your application.
        syslog.syslog(level, '__name__: %s:' % msg)

    def logdbg(msg):
        logmsg(syslog.LOG_DEBUG, msg)

    def loginf(msg):
        logmsg(syslog.LOG_INFO, msg)

    def logerr(msg):
        logmsg(syslog.LOG_ERR, msg)
```

Now you can just use the functions `logdbg()`, `loginf()`, and `logerr()` in
your code, secure in the knowledge that it will work under WeeWX V3, as
well as V4 and V5.