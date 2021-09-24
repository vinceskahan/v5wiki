# How to run multiple instances of WeeWX

There are a few applications where you might want to run more than one instance of WeeWX.  You might want to test a new version of WeeWX using the simulator without disrupting data from your weather station.  Or you might have more than one weather station from which you would like to collect data.  This is how to do it.

## Multiple weather stations, one WeeWX installation

You can run any number of WeeWX instances from a single WeeWX installation.  However, each WeeWX instance must have its own:

* configuration file
* database
* reports

Let's say that you have two weather stations connected to a single computer, one station is a Davis Vantage Pro installed at a house and the other an Acurite station installed in a paddock.  We will identify them as 'house' and 'paddock'.  The instructions vary slightly depending on whether you installed WeeWX as a package or via the setup.py approach.  These variations are highlighted where applicable.

1. Create the configuration files.  The config files may be named whatever you wish; however, keep in mind that the file name stem will be used later with the init script to control each instance and in the log entries. Typically, a self-evident descriptive single word works best. Create one file for each station, say `house.conf` and `paddock.conf`.  Put these files in the directory `/etc/weewx`:

For setup.py installs:
```
sudo mkdir /etc/weewx
sudo cp /home/weewx/weewx.conf /etc/weewx/house.conf
sudo cp /home/weewx/weewx.conf /etc/weewx/paddock.conf
```

For package installs: 
```
sudo cp /etc/weewx/weewx.conf /etc/weewx/house.conf
sudo cp /etc/weewx/weewx.conf /etc/weewx/paddock.conf
```

2. Verify the driver configurations.  In each configuration file, ensure that `station_type` is set correctly.  In `house.conf`:

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

3. Verify the database configurations.  In each configuration file, ensure a unique database.  In `house.conf`:

```
[Databases]
        [[archive_sqlite]]
            database_name = house.sdb
            database_type = SQLite
        [[archive_mysql]]
            database_name = house
            database_type = MySQL
```
and in `paddock.conf`:

```
[Databases]
        [[archive_sqlite]]
            database_name = paddock.sdb
            database_type = SQLite
        [[archive_mysql]]
            database_name = paddock
            database_type = MySQL
```

4. Verify the report configurations.  In each configuration file, ensure that HTML_ROOT is unique.  In `house.conf`:

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

5. Configure the startup script.  Use the `weewx-multi` init script to start each instance when the computer boots. The `weewx-multi` init script can be found in `/home/weewx/util/init.d` if WeeWX was installed with the setup.py approach.  If WeeWX was installed as a package you will need to download `weewx-multi` from the WeeWX GitHub repo.

For setup.py installs:
```
sudo cp util/init.d/weewx-multi /etc/init.d/weewx
sudo update-rc.d weewx defaults
echo 'WEEWX_INSTANCES="house paddock"' | sudo tee /etc/default/weewx-multi
echo 'WEEWX_CFGDIR=/etc/weewx' | sudo tee -a /etc/default/weewx-multi
```

For package installs:
```
wget -P /var/tmp https://raw.githubusercontent.com/weewx/weewx/master/util/init.d/weewx-multi
sudo cp /var/tmp/weewx-multi /etc/init.d/weewx
sudo update-rc.d weewx defaults
echo 'WEEWX_INSTANCES="house paddock"' | sudo tee /etc/default/weewx-multi
echo 'WEEWX_CFGDIR=/etc/weewx' | sudo tee -a /etc/default/weewx-multi
echo 'WEEWX_BINDIR=/usr/share/weewx' | sudo tee -a /etc/default/weewx-multi
```

### Starting and stopping

Now you can start and stop each instance like this:

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

The log entries for each instance will be uniquely identified.  For example, log entries from the house instance will contain `weewx-house` and log entries from the paddock instance will be identified by `weewx-paddock`. By default, log entries for each instance will be written to the same log file. This can make reading the log file and troubleshooting difficult, especially so if WeeWX log entries are written to the system log. You may wish to consider configuring the logging system to store WeeWX log entries in a separate log file, or even having log entries for each WeeWX instance written to separate files. Refer to the [Save WeeWX log messages to a separate file](logging.md) wiki page.    

## Multiple WeeWX versions

TODO: document this
