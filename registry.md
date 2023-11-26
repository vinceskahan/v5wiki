# The Station Registry

When a station is registered it will show up on the map:

http://weewx.com/stations.html

You must opt-in to register your station(s).  WeeWX never sends data until you
tell it to do so.

Stations are placed on the map using their location, and, if possible, a screen
capture is made of the station's URL.

## How to turn it on?

In the weeWX configuration file, enable the `StationRegistry`:

```
[StdRESTful]
    [[StationRegistry]]
        register_this_station = True
```

The `station_url` must be specified.

## How to know if it is working?

Look at the log.  You should see messages about `StationRegistry`.  If
registration fails, the reason will appear in the log file.

```
grep StationRegistry /var/log/syslog
```

## How does it work?

Each time WeeWX starts, or once a day if WeeWX has been running, the
`StationRegistry` service sends a JSON string to the registry.

Stations are identified by a URL.  If you register multiple stations, each must
have its own URL.  URLs that are not publicly accessible are OK, but in that
case no capture is possible.

Captures are made of each `station_url` periodically.

Stations that have not contacted the registry within 30 days will be removed
from the listing.

## What information is sent to the registry?

The StationRegistry service sends the following information:

* station URL
* latitude and longitude
* weewx version
* python version
* platform information (the string you see when you do 'uname -a')
* the location of `weewxd` (used to infer the install method)
* the location of `weewx.conf` (used to infer the install method)

