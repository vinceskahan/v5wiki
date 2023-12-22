These are things that you should know and do before you post to the [weewx-user group](https://groups.google.com/forum/#!forum/weewx-user).

## What is supported

WeeWX is a free, open-source project created and run entirely by volunteers. We try
to provide support on weewx-user, but it is not guaranteed. You are dependent
on the kindness of strangers, so please be nice to them!

## Do your homework

Chances are your problem has been encountered by someone else. So before you post,
do a search in the weewx-user group.

## Do not just use someone else's posting

If you find another posting that is similar to your problem, it might be OK
to continue that thread. However, if the posting is older than a few months,
or if it is only slightly related to your problem, you should create a new
thread.  Then make a link to the other posting, if it helps clarify the problem.

## Do not cross-post

If you post in weewx-user, do not post the same message in weewx-development.
Cross-posting almost ensures that your request will be ignored.

## Write a good bug report

A good bug report includes:
* what are you trying to do
* what did you expect to see (or happen)
* what did you actually see (or what happened)
* what is the configuration of your system
* what did you change (if anything)

### A bad bug report
```
I tried to install WeeWX and it doesn't work.  Will somebody help me ?
```

### A good bug report
```
I'm trying to enable realtime updates to my Belchertown skin and it is 
not updating in realtime.  I keep getting 'waiting for data'.  I am 
running weewx 4.5.1 installed via apt-get and Belchertown 1.3.0 on a 
pi4 under Raspbian. My os version from /etc/os-release is 10.4
```

## The WeeWX debug utility

The debug utility (`wee_debug` or `weectl debug`) can output more detailed information about your system.  You may be asked to run that utility and follow up to your original problem report with more details.

Note that the utility *tries* to obfuscate usernames/passwords/keys so that posting your debug does not post private information.  However, you still need to examine the output to make sure it didn't miss any usernames, passwords, tokens, or other private information.

## Include the log!

There are three important rules to remember when seeking help:

1. Include the log!
2. Include more of the log!
3. Did you include the log?

As it runs, WeeWX sends messages to the system log. If you look at the messages in
the log, you will probably figure out exactly what is causing the problem.  If you
do not understand what is happening in the log messages, someone else probably will.
But without the log, no one else can help you.

Too often, we see either nothing from the log, or a tiny sliver of text with no context. 

_**Do not send a screenshot!**_

A screenshot is hard to read, includes only a couple dozen lines, cannot be searched nor
translated, and waste a lot of space. _Send only text!_

The wiki article [*How to view the log*](view-logs) explains where to find the log on
your system and how to view it.

## How to get a good, useful log

It is helpful to have debug enabled before you run WeeWX, as that results in more
verbosity in the log messages.  This is how to enable debug and get the contents
of the log.

1. Figure out where the log is located

    | system                        | location of system log |
    |-------------------------------|------------------------|
    | Debian, Ubuntu, Raspian       | /var/log/syslog        |
    | Redhat, Fedora, CentOS, Rocky | /var/log/messages      |
    | MacOS                         | /var/log/syslog        |
    
2. Stop `weewxd`

    | init system | how to stop weewxd          |
    |-------------|-----------------------------|
    | systemd     | sudo systemctl stop weewx   |
    | sysV        | sudo /etc/init.d/weewx stop |
    | launchd     | sudo launchctl unload /Library/LaunchDaemons/com.weewx.weewxd.plist |
 
3. Set `debug=1` in the configuration file in `weewx.conf`. Setting `debug=2`
   will give you even more information, which can be helpful when debugging
   RESTful uploads and FTP. However, it will generate a lot of log entries.
 
4. View and save the system log

    In a terminal window, use `tail` to watch the system log, combined
    with `tee` to save what you see to a separate file:

       tail -f /var/log/syslog | tee /var/tmp/mylog

   This will allow you to see the log, while saving a copy to the file
   `/var/tmp/mylog`. On recent Linux systems you might have to use `sudo`
   to view the log. In this case, the command becomes:

       sudo tail -f /var/log/syslog | tee /var/tmp/mylog
 
   NOTE - On some systems that use systemd, you might not see the system log
   because systemd-journald intercepts all of the system logging.  If that is
   the case on your system, you will have to use the `journalctl` command to
   view the log. This example returns the last 20 lines of weewx log messages:

       sudo journalctl -u weewx --no-pager -n 20

5. Start `weewxd` and watch the log

    In a _different_ terminal, start `weewxd` as you watch the log
    output in the first terminal.

    | init system | how to stop weewxd          |
    |-------------|-----------------------------|
    | systemd     | sudo systemctl start weewx   |
    | sysV        | sudo /etc/init.d/weewx start |
    | launchd     | sudo launchctl load /Library/LaunchDaemons/com.weewx.weewxd.plist |
 
6. Let it run through at least the first reporting cycle (usually 5-10 minutes).
 
7. Attach the created file (`/var/tmp/mylog`) to your post to weewx-user, or put
   it on a cloud service like [pastebin.com](http://pastebin.com/) and include a
   link.
