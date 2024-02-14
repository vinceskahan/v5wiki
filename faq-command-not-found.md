### Problem Description
Users new to Linux/Unix who have installed WeeWX via the 'pip' method are frequently confused when they run a command as indicated in the WeeWX documentation and get a 'command not found' message.

    pi@pi4p1:~ $ weectl extension list
    weectl: command not found

The problem here is that pip installations of weewx require you to activate the python virtual environment before running weewx commands

#### The correct way

Activate your python virtual environment first...

    pi@pi4p1:~ $ source ~/weewx-venv/bin/activate
    pi@pi4p1:~ weectl extension list
    Extension Name    Version   Description
    mqtt              0.23      Upload weather data to MQTT server.


#### For more information

* for $PATH details, see the wiki page [Understanding paths](Understanding-paths)
* for where things are located, see the [User's Guide](https://weewx.com/docs/usersguide.htm#Where_to_find_things)
