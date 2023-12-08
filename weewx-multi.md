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

If you installed WeeWX using a DEB/RPM package, the unit template file is already installed.  Just enable each `weewxd` instance.
```
sudo systemctl enable weewx@vantage
sudo systemctl enable weewx@paddock
```

If you installed WeeWX using `pip`, copy the unit template into place, then then enable each `weewxd` instance.
```
sudo cp ~/weewx-data/util/systemd/weewx@.service /etc/systemd/system
sudo systemctl enable weewx@vantage
sudo systemctl enable weewx@paddock
```

#### WeeWX V4 using systemd unit

Download the unit template
```
wget -P /var/tmp https://raw.githubusercontent.com/weewx/weewx/v5.0/util/systemd/weewx@.service
```
Open the unit template with a text editor, and modify the `ExecStart` line to match your installation.
```
# for a DEB/RPM package installation:
ExecStart=weewxd --log-label weewx-%i /etc/weewx/%i.conf
# for a setup.py installation:
ExecStart=/home/weewx/bin/weewxd --log-label weewx-%i /home/weewx/%i.conf
```
Copy the unit template into place
```
sudo cp /var/tmp/weewx@.service /etc/systemd/system
```
Enable each `weewxd` instance.
```
sudo systemctl enable weewx@vantage
sudo systemctl enable weewx@paddock
```

#### WeeWX V4 using sysV init

Download the `weewx-multi` init script, and copy it into place.
```
wget -P /var/tmp https://raw.githubusercontent.com/weewx/weewx/v4.10.2/util/init.d/weewx-multi
sudo cp /var/tmp/weewx-multi /etc/init.d/weewx
```
Create the `defaults` file to match your installation.

For a DEB/RPM package installation:
```
echo 'WEEWX_INSTANCES="house paddock"' | sudo tee /etc/default/weewx-multi
echo 'WEEWX_CFGDIR=/home/weewx' | sudo tee -a /etc/default/weewx-multi
echo 'WEEWX_BINDIR=/home/weewx/bin' | sudo tee -a /etc/default/weewx-multi
```
For a `setup.py` installation:
```
echo 'WEEWX_INSTANCES="house paddock"' | sudo tee /etc/default/weewx-multi
echo 'WEEWX_CFGDIR=/etc/weewx' | sudo tee -a /etc/default/weewx-multi
echo 'WEEWX_BINDIR=/usr/share/weewx' | sudo tee -a /etc/default/weewx-multi
```
Finally, enable the script
```
sudo update-rc.d weewx defaults
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

The log entries for each instance will be uniquely identified.  For example, log entries from the house instance will contain `weewx-house` and log entries from the paddock instance will be identified by `weewx-paddock`. By default, log entries for each instance will be written to the same log file. This can make reading the log file and troubleshooting difficult, especially so if WeeWX log entries are written to the system log. You may wish to consider configuring the logging system to store WeeWX log entries in a separate log file, or even having log entries for each WeeWX instance written to separate files. Refer to the [Save WeeWX log messages to a separate file](https://github.com/weewx/weewx/wiki/logging#save-weewx-log-messages-to-a-separate-file) wiki page.    
