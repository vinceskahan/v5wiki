## How to run weewx as a non-root user

By default, weewx runs as root.  This guide illustrates how to run weewx as a non-root user.  It assumes that weewx has been installed to /home/weewx using setup.py and works properly when run as the root user.

1.  Stop weewx if it is running

`sudo /etc/init.d/weewx stop`

2.  Create the user account

`sudo useradd wxuser`

3.  In the startup script file, /etc/init.d/weewx, specify the user, and a location for
the process ID (PID) file where that user has write privileges
~~~~
WEEWX_USER = wxuser:wxuser
PIDFILE = $WEEWX_ROOT/run/$NAME.pid
~~~~
4.  Create the directory where the PID file will reside

`sudo mkdir /home/weewx/run`

5.  Give the user ownership of all weewx files

`sudo chown -R wxuser.wxuser /home/weewx`

6.  Give serial/usb access to the user with a udev rule

    For example, for a vantage pro connected via USB, create the file

`    /etc/udev/rules.d/vpro.rules`

with the following contents:

`    SUBSYSTEM=="usb", ATTRS{interface}=="CP2102 USB to UART Bridge Controller", MODE: = "664", GROUP = "wxuser"`

7.  Start weewx

`sudo /etc/init.d/weewx start`