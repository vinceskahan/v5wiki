# How to run multiple instances of WeeWX

There are a few applications where you might want to run more than one instance of WeeWX.  You might want to test a new version of WeeWX using the simulator without disrupting data from your weather station.  Or you might have more than one weather station from which you would like to collect data.  This is how to do it.

You can run any number of WeeWX instances from a single WeeWX installation.  However, each WeeWX instance must have its own:

* configuration file
* database
* reports

The instructions vary slightly depending on where your WeeWX configuration file, database, and reports are located.

## Multiple weather stations, one WeeWX installation

Let's say that you have two weather stations connected to a single computer, one station is a Davis Vantage Pro installed at a house and the other an Acurite station installed in a paddock.  We will identify them as 'house' and 'paddock'.

### 1. Create the configuration files

The config files may be named whatever you wish; however, keep in mind that the file name stem will be used later with the init script to control each instance and in the log entries. Typically, a self-evident descriptive single word works best. Create one file for each station, say `house.conf` and `paddock.conf`.  Put these files in the same directory as your original `weewx.conf`.

For DEB/RPM package installs: 
```
sudo cp /etc/weewx/weewx.conf /etc/weewx/house.conf
sudo cp /etc/weewx/weewx.conf /etc/weewx/paddock.conf
```
For pip installs:
```
cp ~/weewx-data/weewx.conf ~/weewx-data/house.conf
cp ~/weewx-data/weewx.conf ~/weewx-data/paddock.conf
```
For setup.py installs:
```
sudo cp /home/weewx/weewx.conf /home/weewx/house.conf
sudo cp /home/weewx/weewx.conf /home/weewx/paddock.conf
```


### 2. Verify the driver configurations

In each configuration file, ensure that `station_type` is set correctly.  

For example, in `house.conf`:
```
[Station]
    ...
    station_type = Vantage
[Vantage]
    ...
```
and in `paddock.conf`:
```
[Station]
    ...
    station_type = Acurite
[Acurite]
    ...
```

### 3. Verify the database configurations

In each configuration file, ensure a unique database.

For example, in `house.conf`:
```
[Databases]
    [[archive_sqlite]]
        database_name = house.sdb
        database_type = SQLite
```
and in `paddock.conf`:
```
[Databases]
    [[archive_sqlite]]
        database_name = paddock.sdb
        database_type = SQLite
```

### 4. Verify the report configurations

In each configuration file, ensure that HTML_ROOT is unique.

For example, in `house.conf`:
```
[StdReport]
    HTML_ROOT = public_html/house
    ...
```
and in `paddock.conf`:
```
[StdReport]
    HTML_ROOT = public_html/paddock
    ...
```

### 5. Configure the startup script

#### WeeWX V5 using systemd unit template

If you installed WeeWX using a DEB/RPM package, or if you installed using `pip` and configured WeeWX to run as a daemon, then the unit template file is already installed.  Just enable each `weewxd` instance.
```
sudo systemctl enable weewx@vantage
sudo systemctl enable weewx@paddock
```

#### WeeWX V5 using sysV init

In the defaults file `/etc/default/weewx` file, set the `WEEWX_INSTANCES` variable to match your installation.
```
sudo sed -i 's/^WEEWX_INSTANCES=.*/WEEWX_INSTANCES=\"house paddock\"/' /etc/default/weewx
```

### Starting and stopping

Now you can start and stop each instance.

#### Using systemd
```
# start each instance
sudo systemctl start weewx@vantage
sudo systemctl start weewx@paddock

# stop just the paddock
sudo systemctl stop weewx@paddock

# restart only the vantage instance
sudo systemctl restart weewx@vantage

# stop all instances
sudo systemctl stop weewx
```

#### Using the sysV init
```
# check status of every instance
/etc/init.d/weewx status

# start only the paddock instance
sudo /etc/init.d/weewx start paddock

# restart only the paddock instance
sudo /etc/init.d/weewx restart paddock

# stop only the house instance
sudo /etc/init.d/weewx stop house
```

### Logging

The log entries for each instance will be uniquely identified.  For example, log entries from the house instance will contain `weewxd-house` and log entries from the paddock instance will be identified by `weewxd-paddock`. By default, log entries for each instance will be written to the same log file. This can make reading the log file and troubleshooting difficult, especially so if WeeWX log entries are written to the system log. You may wish to consider configuring the logging system to store WeeWX log entries in a separate log file, or even having log entries for each WeeWX instance written to separate files. Refer to the [Save WeeWX log messages to a separate file](logging#multiple-weewx-log-files) wiki page.    
