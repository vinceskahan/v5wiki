##FIXME: rewrite for v5 pip
### Problem Description
Users new to Linux/Unix who have installed WeeWX via the 'setup.py' method are frequently confused when they run a command as indicated in the WeeWX documentation and get a 'command not found' message.

    pi@pi4p1:~ $ sudo weectl extension list
    sudo: weectl: command not found

The problem here is that setup.py installations of weewx install things in /home/weewx/bin which is not in the user's $PATH.

    pi@pi4p1:~ $ echo $PATH
    /home/pi/.local/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/local/games:/usr/games

#### The correct way

Preface the command with the full pathname to the utility you are trying to use.

    pi@pi4p1:~ $ sudo /home/weewx/bin/wee_extension --list
    Extension Name    Version   Description
    mqtt              0.23      Upload weather data to MQTT server.


#### For more information

* for $PATH details, see the wiki page [Understanding paths](Understanding-paths)
* for where things are located, see the [User's Guide](https://weewx.com/docs/usersguide.htm#Where_to_find_things)
