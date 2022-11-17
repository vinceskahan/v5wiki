# Overview
Earlier versions of WeeWX use Python's `syslog` module to log events. It has
several disadvantages:
- It works only under versions of *nix. No Windows.
- It can log only to the system log.
- It is inflexible in the formatting it uses.

WeeWX V4 will transition to using Python's
[`logging`](https://docs.python.org/3/library/logging.html) package. It has
several advantages:
- It abstracts out logging destinations, formatting, and filtering, allowing all
  to be changed at runtime.
- It has destinations that work on Windows.
- It can support email or socket logging.

In short, it is a much more flexible facility. Plus, it's easy to use.

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
_New: the `rotate` handler is now included in Version 4.10_ 

With that in mind, here is the default configuration that WeeWX uses (Linux
only; the defaults are slightly different for MacOS. See [below](#for-mac-users).)

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

        # Log to a set of rotating files
        [[[rotate]]]
            level = DEBUG
            formatter = standard
            class = logging.handlers.RotatingFileHandler
            # Writing to this file will require root privileges:
            filename = /var/log/weewx.log
            maxBytes = 10000000
            backupCount = 4

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
   destination. There are three handlers that are included in the default
   configuration: `syslog`, `console`, and `rotate`.
3. Formatters, which specify the layout of log records in the final output. A
   number of attributes are available to the formatter (such as `%(levelname)s`; 
   see the documentation [*LogRecord attributes*](https://docs.python.org/3/library/logging.html#logrecord-attributes) 
   for a complete list).

The value for `{log_level}` depends on whether the `debug` option has
been set. If it has not (the default), then `log_level` is `INFO`, otherwise,
`DEBUG`.

The value for `{process_name}` is passed in when setting up the logging
facility. For the WeeWX main program, it is `weewxd` (see below).

## Specifying other handlers

An example. Say you want logs to go to not only the system log (the default),
but to the console as well. Then add this to your `weewx.conf` file:

```ini
[Logging]
  [[root]]
    handlers = syslog, console
```

This will override the default list of handlers, which consists only of
`syslog`, with a new list that includes `console` as well as `syslog`.

## Customizing what gets logged

Another example. When the `debug` option is on, the default "root" logger will
show `DEBUG` messages and above --- basically everything. This is fine, but say
you've decided that the `weewx.restx` module is too chatty for your liking. For
it, you want to see only `INFO` messages and above --- nothing else.

The solution is to tailor the logger used by the `weewx.restx` module, so it no
longer inherits properties from the root logger. For this logger, we will
specify that it logs only `INFO` and above. To do this, the `[Logging]` section
would look like:

```ini
[Logging]
  [[loggers]]
    [[[weewx.restx]]]
      level = INFO
```

Or instead, suppose you are debugging the `weewx.restx` module. In this case it
would be desirable to set its logging `level` to `DEBUG` while other loggers
continue to log `INFO` or higher. Again, the solution is to customize the logger
for the `weewx.restx` module.  In this case, along with setting the logging
`level`, we need to define a `handler`. Because the `weewx.restx` logger will
handle the logging for the `weewx.restx` module, we will also want to turn
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

## Logging to rotating files

By default, the logging module logs to the "system log". In some cases, you may
want to log to a set of rotating log files, such as `/var/log/weewx.log`. The
WeeWX logging facility allows you to do this.

Add this to `weewx.conf`:

```ini
[Logging]
    # Root logger
    [[root]]
      handlers = rotate,
```

This tells the logging facility to use the `rotate` handler, instead of the
default `syslog` handler. The trailing comma is important: it tells Python that
the option is actually a list.

By default, the rotating log file will be `/var/log/weewx.log`. If you wish to
change this to something else, use this in `weewx.conf`:

```ini
[Logging]
    # Root logger
    [[root]]
      handlers = rotate,
      
    [[handlers]]
        [[[rotate]]]
            filename = /var/tmp/weewx.log  
```

As before, this will cause the `rotate` handler to be used, but this time, the
log file will be `/var/tmp/weewx.log`.
 
The `rotate` handler also serves as a good example of overriding default logging
behavior.

## For Mac users.

The default root handler under MacOS is `rotate`, instead of `syslog`.
This is because the system logger on the Mac is harder to use for debugging, so
we log to rotating files instead.


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

## In main programs

If you are writing a utility which will be run as the "main program", then there
are a couple extra steps you must take. At the minimum, include the following at
the top of your code:

```python
import logging
import weeutil.logger

log = logging.getLogger(__name__)

weeutil.logger.setup('prog_name', {})
```

This will set up a default logging configuration, suitable for most situations.

However, if you want the user to be able to customize the logging for your
utility, then you need a few extra steps:

```python
    import logging
    import weecfg
    import weeutil.logger
    from weeutil.weeutil import to_int

    log = logging.getLogger(__name__)

    # Temporarily set up logging using the defaults. This is so you can log
    # the process of reading in the weewx.conf file
    weeutil.logger.setup('utility_name', {})

    ...

    # This message will use the default logger:
    log.info("Attempting to read config file from %s' % config_path)

    # Get the config_dict to use
    config_path, config_dict = weecfg.read_config(options.config_path, args)

    # Set weewx.debug as necessary:
    weewx.debug = to_int(config_dict.get('debug', 0))

    # Now we can set up the user customized system:
    weeutil.logger.setup('utility_name', config_dict)

    # This message will use the customized logger:
    log.info("Successfully read in weewx.conf")
```

Note how the pattern first sets up a temporary logging facility, using WeeWX
defaults. This is to log the process of reading in the config file. Then, once
that is done, it uses the fully customized version.

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
your code, secure in the knowledge that it will work under both WeeWX V3 and V4.
