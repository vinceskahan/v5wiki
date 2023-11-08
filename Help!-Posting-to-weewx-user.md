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

### _**Do not send a screenshot!**_

They are hard to read, include only a couple dozen lines, cannot be searched nor
translated, and waste a lot of space. _Send only text!_

To get a good, useful log:

1. Figure out where the log is located. On Debian systems, such as the Raspberry Pi,
   this is `/var/log/syslog`. The *User's Guide* has a section [*Where to find
   things*](http://weewx.com/docs/usersguide.htm#Where_to_find_things) that can
   help locate your system log.

2. Stop `weewxd`. On Debian systems, this can be done with

       sudo systemctl stop weewx
 
3. Set `debug=1` in the configuration file in `weewx.conf`. Setting `debug=2`
   will give you even more information, which can be helpful when debugging
   RESTful uploads and FTP. However, it will generate a lot of log entries.
 
4. In a console terminal, type the command 

       tail -f /var/log/syslog | tee /var/tmp/mylog

   This will allow you to see the log, while saving a copy to the file
   `/var/tmp/mylog`. One potential complication is that on recent Linux systems
   you might have to use `sudo` to view the log. In this case, the command
   becomes

       sudo tail -f /var/log/syslog | tee /var/tmp/mylog
 

   note - on a modern systemd system you might not see these files present due to
   systemd handling logging.  You might consider installing a traditional syslog
   daemon (for debian: `sudo apt-get install rsyslog`) or alternately use the
   systemd `journalctl` command.  This example returns the last 20 lines of
   weewx logs from systemd:

       sudo journalctl -u weewx --no-pager -n 20

5. In a _different_ console terminal, restart `weewxd`. On Debian, this is 
   generally done with 

       sudo systemctl start weewx
 
6. Let it run through at least the first reporting cycle (usually 5-10 minutes).
 
7. Attach the created file (`/var/tmp/mylog`) to your post to weewx-user, or put
   it on a cloud service like [pastebin.com](http://pastebin.com/) and include a
   link.
