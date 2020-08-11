This is a guideline for requesting help by posting to the [weewx-user group](https://groups.google.com/forum/#!forum/weewx-user).

# What is supported

WeeWX is a free, open-source project created and run entirely by volunteers. We try
to provide support on weewx-user, but it is not guaranteed. You are dependent
on the kindness of strangers, so please be nice to them!

Drivers and skins that are not part of the standard distribution will be given a 
lower priority.

# The system log

There are three important rules to remember when seeking help from the group:

1. Include the log!
2. Include more of the log!
3. Include still more!

Too often, we see either nothing from the log, or a tiny sliver of text with no context. 

### _**Do not send a screen shot!**_

They are hard to read, include only a couple dozen lines, cannot be searched nor translated, and waste a lot of space. Send only text!

To get a good, useful log:

1. Figure out where it is located. On Debian systems, this is `/var/log/syslog`. The *User's Guide* has a section [*Where to find things*](http://weewx.com/docs/usersguide.htm#Where_to_find_things) that can help 
locate your system log.
2. Stop weewx
3. Set `debug=1` in the configuration file in `weewx.conf`. Setting `debug=2` will give you even more information, which can be helpful when debugging RESTful uploads and FTP. However, it will generate a lot
of log entries.
4. In a console terminal, type the command 

    `tail -f /var/log/syslog | tee /var/tmp/mylog`

    This will allow you to see the log, while saving a copy to the file `/var/tmp/mylog`.
    One potential complication is that on recent Linux systems you might have to use `sudo` to view the log. In this case, the command becomes

    `sudo tail -f /var/log/syslog | tee /var/tmp/mylog`
 
5. In a different console terminal, restart weewx. Let it run through at least the first reporting cycle (usually 5-10 minutes)
6. Attach the created file (`/var/tmp/mylog`) to your post to weewx-user, or put it on a cloud service like [pastebin.com](http://pastebin.com/) and include a link.

