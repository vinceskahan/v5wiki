## rtl_433 SDR typical issues

Setting up SDR and RTL-433 confuses many users, especially with respect to getting file-not-found errors when trying to start up WeeWX as a daemon.

What to do is documented in the the [github readme](https://github.com/matthewwall/weewx-sdr) but many new Linux/Unix users miss some of the nuances.

### What is happening
When you install rtl_433 from source, it usually is installed in /usr/local/bin which is not in the operating system's default command $PATH.  The end result is typically users can install, test, and run rtl_433 from the commandline but it doesn't work when they try to start up WeeWX as a daemon.

This is the same issue documented [here](faq-command-not-found) in the wiki.

### How to make it work
Simplest way to work around this is to specify the full path to rtl_433 in your weewx.conf

The example in the driver web page assumes rtl_433 is in a standard place:

```
[SDR]
    driver = user.sdr
    cmd = rtl_433 -M utc -F json -R 17 -R 44 -R 50
```

To explicitly specify the location of rtl_433 simply use a full pathname to the executable:

```
[SDR]
    driver = user.sdr
    cmd = /usr/local/bin/rtl_433 -M utc -F json -R 17 -R 44 -R 50
```