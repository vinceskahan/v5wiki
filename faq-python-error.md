### Problem Description

A user installs weewx v4 and sees python-related errors in the console or in their logs.

Typical example:

    Apr 17 00:02:04 raspberrypi python3[520]: weewx[520] CRITICAL __main__:     ****    File "/usr/share/weewx/user/forecast.py", line 567
    Apr 17 00:02:04 raspberrypi python3[520]: weewx[520] CRITICAL __main__:     ****      except OSError, e:
    Apr 17 00:02:04 raspberrypi python3[520]: weewx[520] CRITICAL __main__:     ****                    ^
    Apr 17 00:02:04 raspberrypi python3[520]: weewx[520] CRITICAL __main__:     ****  SyntaxError: invalid syntax
    Apr 17 00:02:04 raspberrypi python3[520]: weewx[520] CRITICAL __main__:     ****  Exiting.

#### What is happening ?

The underlying issue is trying to run legacy python2 code under the newer python3. Python3 contained some breaking changes in the language, so any legacy python2 code likely needs to be altered slightly in order to run under both major python versions.

The example above mentions 'forecast.py', so in this example the Forecast extension the user is running was written for python2 only.


#### What do I do ?

* upgrade to a python3-compatible version of the driver/extension/skin that is throwing the error

