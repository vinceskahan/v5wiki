# The Station Registry

You can opt-in to have your station(s) in the weeWX station registry.  When a station is registered it will show up on the map:

http://weewx.com/stations.html

Just to be clear: this is an opt-in service.  WeeWX never sends data until you tell it to do so.

## How to turn it on?

In the weeWX configuration file, enable the `StationRegistry`:

```
[StationRegistry]
    enable = True
```

The `station_url` must be specified.

## How to know if it is working?

Look at the log.  You should see messages about `StationRegistry`.  If registration fails, the reason will appear in the log file.

```
grep StationRegistry /var/log/syslog
```

## How does it work?

Each time weeWX starts, or once per week if weeWX has been running, the StationRegistry service sends a JSON string to the registry.

The map is updated periodically to show new stations.

Captures are made of each `station_url` periodically.

Stations that have not contacted the registry within 30 days will be removed from the listing.

## What information is sent to the registry?

The StationRegistry service sends the following information:

* station URL
* latitude and longitude
* weewx version
* python version
* platform information (the string you see when you do 'uname -a')

