By default, weewx runs as root.  This guide illustrates how to run weewx as a non-root user. There are multiple possible installation types. Here we list:
* installation under /home, or
* installation from a Debian repository

In each case, we assume the user and group have been created already, with a name _wxuser_

1.  Create the user account

     ~~~~~
     sudo useradd wxuser
     ~~~~~

2.  Give serial/usb access to the user with a udev rule

    For example, for a vantage pro connected via USB, create the file

     ~~~~~
     /etc/udev/rules.d/vpro.rules
     ~~~~~

    with the following contents:

     ~~~~~
     SUBSYSTEM=="usb", ATTRS{interface}=="CP2102 USB to UART Bridge Controller", MODE: = "664", GROUP = "wxuser"
     ~~~~~
 
  or similarly for the Fine Offset clones on a raspberry pi:

     ~~~~~
     SUBSYSTEM=="usb", ATTR{idVendor}=="1941", ATTR{idProduct}=="8021", MODE="0664", GROUP="wxuser"
     ~~~~~

   the idVendor and idProduct can be established by using the lsusb command.

## Installed under /home, using setup.py
 It assumes that weewx has been installed to `/home/weewx` using setup.py and works properly when run as the root user.

1.  Stop weewx if it is running:

     ~~~~~
     sudo /etc/init.d/weewx stop
     ~~~~~


3.  In the startup script file, `/etc/init.d/weewx`, specify the user, and a location for
the process ID (PID) file where that user has write privileges

     ~~~~~
     WEEWX_USER = wxuser:wxuser
     PIDFILE = $WEEWX_ROOT/run/$NAME.pid
     ~~~~~

4.  Create the directory where the PID file will reside

     ~~~~~
     sudo mkdir /home/weewx/run
     ~~~~~

5.  Give the user ownership of all weewx files

     ~~~~~
     sudo chown -R wxuser:wxuser /home/weewx
     ~~~~~



7.  Start weewx

     ~~~~~
     sudo /etc/init.d/weewx start
     ~~~~~

## Installed using a Debian package

This assumes that weewx has been installed via a process such as 

     ~~~~~
     apt install weewx
     ~~~~~

It also assumes the program startup is under the control of systemd, although even then it is still controlled through the script  `/etc/init.d/weewx`.  If you use a full systemd configuration, then see also [the wiki page relating to systemd](../systemd#to-run-as-a-non-root-user).

1.  Stop weewx if it is running:

     ~~~~~
     systemctl stop weewx 
     systemctl status weewx 
     ~~~~~
     The output of the _status_ command should show you, under "Loaded:" that it is loaded from `/etc/init.d/weewx`

2.  Rather than edit the startup script directly (and risk losing changes each update), we override parameters by creating a file `\etc\default\weewx`, in which we specify the user, and a location for
the process ID (PID) file (which is in a folder where that user has write privileges). Note, on recent Debian systems, /var/run is simply a symlink to /run

     ~~~~~
     WEEWX_USER = wxuser:wxuser
     PIDFILE = /run/$NAME/$NAME.pid
     ~~~~~

4.  The directory where the PID file will reside is on a temporary filesystem, which will be destroyed each reboot.
The answer is to have systemd re-create the folder automatically each time. This is controlled  by the creation of the file `/usr/lib/tmpfiles.d/weewx`, with contents:

     ~~~~~
    # create the directory for the weewx sysV pid file each boot
    d /run/weewx 0755 wxuser wxuser - -
     ~~~~~

5.  Give the user ownership of all sqlite DB files

     ~~~~~
     sudo chown -R wxuser:wxuser /var/lib/weewx
     ~~~~~

7.  Start weewx and check

     ~~~~~
     systemctl start weewx 
     ~~~~~
### Debian 10 and weewx 3.9.2
With the upgrade to Buster the system was changed in a way that pidfiles owned by non-root users are not trusted and you cannot stop or restart the weewx service.
The solution is to edit the init script `\etc\init.d\weewx` and in the functions _do_stop_() and _do_restart_() edit the arguments to the _start-stop-daemon_ command. For example, edit the _do_stop()_ function so that

~~~~~
         instead of:
     start-stop-daemon --stop --pidfile $PIDFILE
         it reads 
     start-stop-daemon --stop --pidfile $PIDFILE --user $WEEWX_USER
~~~~~

and likewise for the do_restart function.

There is a further complexity: the _--user_ parameter cannot take the form of _user:group_ so the easiest way is to simply remove the ":wxuser" part from the definition of WEEWX_USER in `\etc\default\weewx`.
This will not be a problem  if the group name is the default for the weewx user, as the default group is applied automatically. If you require a different group then modify the init.d script accordingly with a suitable WEEWX_GROUP parameter.