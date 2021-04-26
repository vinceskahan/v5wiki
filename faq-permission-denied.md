### Problem Description
Users new to Linux/Unix are frequently confused when they run a command as indicated in the WeeWX documentation and get a 'permission denied' message.

    pi@pi4p1:~ $ /home/weewx/bin/wee_extension --install /var/tmp/gw1000-0.3.1.tar.gz
    Request to install '/var/tmp/gw1000-0.3.1.tar.gz'
    Extracting from tar archive /var/tmp/gw1000-0.3.1.tar.gz
    Traceback (most recent call last):
       [...]
    PermissionError: [Errno 13] Permission denied: '/home/weewx/bin/user/gw1000.py'


The problem here is that weewx installs things as the privileged user 'root', yet the user is using a non-privileged user 'pi' to try to do privileged things.

#### The correct way

Preface the command with 'sudo' so that your command runs with elevated privileges.

    pi@pi4p1:~ $ sudo /home/weewx/bin/wee_extension --install /var/tmp/gw1000-0.3.1.tar.gz
    Request to install '/var/tmp/gw1000-0.3.1.tar.gz'
    Extracting from tar archive /var/tmp/gw1000-0.3.1.tar.gz
    Saving installer file to /home/weewx/bin/user/installer/GW1000
    Saved configuration dictionary. Backup copy at /home/weewx/weewx.conf.20210426101025
    Finished installing extension '/var/tmp/gw1000-0.3.1.tar.gz'

#### For more information

* for more about permissions, see the [User's Guide](https://weewx.com/docs/usersguide.htm#permissions)