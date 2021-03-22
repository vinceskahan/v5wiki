# How to run multiple instances of WeeWX

There are a few applications where you might want to run more than one instance of WeeWX.  You might want to test a new version of WeeWX using the simulator without disrupting data from your weather station.  Or you might have more than one weather station from which you would like to collect data.  This is how to do it.

## Multiple weather stations, one WeeWX installation

You can run any number of WeeWX instances from a single WeeWX installation.  However, each WeeWX instance must have its own:

* configuration file
* database
* reports

Let's say that you have two weather stations connected to a single computer.  Call them vantage and acurite.  The following instructions assume installation was done using the setup.py approach.  They will need a few minor changes to work with DEB or RPM installs.

1. Create the configuration files.  Create one file for each station, say `vantage.conf` and `acurite.conf`.  Put these files in the directory `/etc/weewx`:

For setup.py installs:
```
sudo mkdir /etc/weewx
sudo cp /home/weewx/weewx.conf /etc/weewx/vantage.conf
sudo cp /home/weewx/weewx.conf /etc/weewx/acurite.conf
```

For DEB or RPM package installs: 
```
sudo cp /etc/weewx/weewx.conf /etc/weewx/vantage.conf
sudo cp /etc/weewx/weewx.conf /etc/weewx/acurite.conf
```

2. Verify the driver configurations.  In each configuration file, ensure that `station_type` is set correctly.  In `vantage.conf`:

```
[Station]
        ...
        station_type = Vantage
[Vantage]
        ...
```
and in `acurite.conf`:

```
[Station]
        ...
        station_type = Acurite
[Acurite]
        ...
```

3. Verify the database configurations.  In each configuration file, ensure a unique database.  In `vantage.conf`:

```
[Databases]
        [[archive_sqlite]]
            database_name = vantage.sdb
            database_type = SQLite
        [[archive_mysql]]
            database_name = vantage
            database_type = MySQL
```
and in `acurite.conf`:

```
[Databases]
        [[archive_sqlite]]
            database_name = acurite.sdb
            database_type = SQLite
        [[archive_mysql]]
            database_name = acurite
            database_type = MySQL
```

4. Verify the report configurations.  In each configuration file, ensure that HTML_ROOT is unique.  In `vantage.conf`:

```
[StdReport]
        HTML_ROOT = public_html/vantage
        ...
```
and in `acurite.conf`:

```
[StdReport]
        HTML_ROOT = public_html/acurite
        ...
```

5. Configure the startup script.  Use the `weewx-multi` init script to start each instance when the computer boots. The `weewx-multi` init script can be found in `/home/weewx/util/init.d` if WeeWX was installed with the setup.py approach, if WeeWX was installed as a DEB or RPM package you will need to download `weewx-multi` from the WeeWX GitHub repo.

For setup.py installs:
```
sudo cp util/init.d/weewx-multi /etc/init.d/weewx
sudo update-rc.d weewx defaults
echo 'WEEWX_INSTANCES="vantage acurite"' | sudo tee /etc/default/weewx-multi
echo 'WEEWX_CFGDIR=/etc/weewx' | sudo tee -a /etc/default/weewx-multi
```

For DEB or RPM package installs:
```
wget -P /var/tmp https://raw.githubusercontent.com/weewx/weewx/master/util/init.d/weewx-multi
sudo cp /var/tmp/weewx-multi /etc/init.d/weewx
sudo update-rc.d weewx defaults
echo 'WEEWX_INSTANCES="vantage acurite"' | sudo tee /etc/default/weewx-multi
echo 'WEEWX_CFGDIR=/etc/weewx' | sudo tee -a /etc/default/weewx-multi
```

### Starting and stopping

Now you can start and stop each instance like this:

```
# check status of every instance
/etc/init.d/weewx status

# start only the acurite instance
sudo /etc/init.d/weewx start acurite

# stop only the vantage instance
sudo /etc/init.d/weewx stop vantage
```

### Logging

The log entries for each instance will be uniquely identified.  For example, log entries from the vantage instance will contain `weewx-vantage` and log entries from the acurite instance will be identified by `weewx-acurite`.

## Multiple WeeWX versions

TODO: document this
