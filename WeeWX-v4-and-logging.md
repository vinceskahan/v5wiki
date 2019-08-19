# Overview
Earlier versions of WeeWX use Python's `syslog` module to log events. It has several disadvantages:
- It works only under versions of *nix. No Windows.
- It can log only to the system log.
- It is inflexible in the formatting it uses.

WeeWX V4 will transition to using Python's [`logging`](https://docs.python.org/3/library/logging.html) package. It has several advantages:
- It abstracts out logging destinations, formatting, and filtering, allowing all to be changed at runtime.
- It has destinations that work on Windws.
- It can support email or socket logging.

In short, it is a much more flexible facility. Plus, it's easy to use.

This is a guide to how `logging` is implemented within WeeWX.

# Configuring logging
The big advantage of the `logging` module is its ability to be extensively customized. By default, WeeWX makes sensible choices, but you may want to change them. This section is on how to do this.

First, read, or, at least, attempt to read, the section on the [schema of the configuration dictionary](https://docs.python.org/3/library/logging.html#module-logging) that `logging` uses. It's dense and hard to follow, but it will eventually sink in. 

With that in mind, here is the default configuration that WeeWX uses (Linux only; the defaults are slightly
different on the Mac or Windows):

```ini
[Logging]
  version = 1
  disable_existing_loggers = False
      
  [[loggers]]
    # Root logger
    [[[root]]]
      level = {log_level}
      propagate = 1
      handlers = syslog,

  # Definitions of possible logging destinations
  [[handlers]]

    # System logger
    [[[syslog]]]
      level = DEBUG
      formatter = standard
      class = logging.handlers.SysLogHandler
      address = /dev/log
      facility = user

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
      format = %(levelname)s %(message)s
    [[[standard]]]
      format = "{process_name}[%(process)d]/%(levelname)s %(name)s: %(message)s" 
    [[[verbose]]]
      format = "%(asctime)s  {process_name}[%(process)d]/%(levelname)s %(name)s: %(message)s"
      # Format to use for dates and times:
      datefmt = %Y-%m-%d %H:%M:%S
```
The value for `{log_level}` depends on whether or not the `debug` option has been set. If it has not (the default), then `log_level` is `INFO`, otherwise, `DEBUG`.

The value for `{process_name}` is passed in when setting up the logging facility. For the WeeWX main program, it is `weewxd` (see below).

An example. Say you want to log to not only the system log (the default), but to the console as well. Then add this to your `weewx.conf` file:

```ini
[Logging]
  [[loggers]]
    [[[root]]]
      handlers = syslog, console
```
This will override the default list of handlers, which consists only of `syslog`, with a new list, that includes `console` as well as `syslog`.

Another example. Say you've decided that the `restx` module is too chatty for your liking when `debug` is on. You want to see only `INFO` messages and above --- nothing else. However, when `debug` is on, the default "root" logger will show `DEBUG` messages above ---basically everything.

The solution is to create a specialized logger for just the `restx` module, so it no longer inherits properties from the root logger. For this logger, we will specify that it logs only `INFO` and above. To do this, the `[Logging]` section would look like:

```ini
[Logging]
  [[loggers]]
    [[[weewx.restx]]]
      level = INFO
```

# Using `logging` in modules
It's very easy to use `logging` from within a module, such as a new service, search list extension, etc. At the top of your code, include

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

# In main programs
If you are writing a utility which will be run as the "main program", then there are a couple extra steps you must take. At the minimum, include the following at the top of your code:

```python
import logging
import weeutil.logger

log = logging.getLogger(__name__)

weeutil.logger.setup('prog_name', {})
```

This will set up a default logging configuration, suitable for most situations.

However, if you want the user to be able to customize the logging for your utility, then you need a few extra steps:

```python
    import logging
    import weecfg
    import weeutil.logger
    from weeutil.weeutil import to_int

    log = logging.getLogger(__name__)

    # Set up logging using the defaults. This is so you can log
    # the process of reading in the weewx.conf file
    weeutil.logger.setup('utility_name', {})

    ...

    # This will use default logger:
    log.info("Attempting to read config file from %s' % config_path)

    # Get the config_dict to use
    config_path, config_dict = weecfg.read_config(options.config_path, args)

    # Set weewx.debug as necessary:
    weewx.debug = to_int(config_dict.get('debug', 0))

    # Now we can set up the user customized system:
    weeutil.logger.setup('utility_name', config_dict)

    # This will use the customized logger:
    log.info("Successfully read in weewx.conf")
```

# Throttling logging events
The Python `logging` module makes it very easy to temporarily reduce the number of uninteresting messages going into a log. 

Say you're about to call a function which will insert hundreds of records into the database using the `addRecord()` method of the `Manager` class. This method logs an `INFO` event for every insertion, resulting in hundreds of useless entries. You'd like to temporarily avoid this. Here's how:

```python
import logging

# Temporarily disable logging for events at or below INFO
logging.disable(logging.INFO)

generate_zillions_of_records()

# Remove the temporary restriction
logging.disable(logging.NOTSET)
```

See the Python docs for more details about [`logging.disable()`](https://docs.python.org/3/library/logging.html#logging.disable).

