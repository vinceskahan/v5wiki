### Cumulus WXNow

This is an extension to weewx that emits data to a file in the format used by Cumulus wxnow.txt.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-cwxn-0.4.tgz

### How to Install

1.  Run the extension installer:

    ```
setup.py install --extension weewx-cwxn-x.y.tgz
```

2.  Modify weewx.conf:

    ```
    [CumulusWXNow]
        filename = /var/tmp/wxnow.txt
```

3.  Restart weewx

    ```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Verify

The wxnow.txt file should be modified on each LOOP.  For some stations the update will happen more often than others.  For example, Vantage sensors update as often as every 2.5 seconds, whereas FineOffset stations never update more often than 48 seconds.

```
Aug 14 2014 23:14
225/003g003t059r000p000P000h081b10097
```