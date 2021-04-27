## Where are my logs ?

Linux/unix systems typically save logs in one of two usual locations:

* /var/log/syslog
* /var/log/messages

The default configuration of most operating systems will result in some WeeWX data being logged twice, some in each logfile.  This is not a bug in either WeeWX or the operating system.  It's just a nuance based on the default system logging configuration of the operating system.

For a raspberry pi running the official RaspiOS (aka Raspbian), looking in 'syslog' is usually the best choice.
