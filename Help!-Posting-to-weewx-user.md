This is a guideline for requesting help by posting to the [weewx-user group](https://groups.google.com/forum/#!forum/weewx-user).

There are three important rules to remember when seeking help from the group:

1. Include the log!
2. Include more of the log!
3. Include still more!

Too often, we see either nothing from the log, or a tiny sliver of text with no context. 

To get a good, useful log:

1. Stop weewx
2. Set `debug=1` in the configuration file in `weewx.conf`
3. Restart weewx

If you are having problems getting weewx to run, or if you are having problems getting a feature to work, be sure to post the log from when weewx starts up. This is because the start up sequence leaves behind important clues as to which services are being loaded and whether they ran to completion successfully. Include the log through at least the first reporting cycle.

If you are having problems with the reporting engine, then it's probably OK to leave out the startup sequence, and just post from where a new archive record comes in.

If the log seems unduly big, you can either include it as an attachment, or put it on a cloud service like [pastbin.com](http://pastebin.com/)

On most Linux systems you can find the log at `/var/log/syslog`