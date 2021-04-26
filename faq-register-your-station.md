
### OPTIONAL registration of WeeWX stations

WeeWX has an **OPTIONAL** capability to register your station with the weewx.com servers, once per week.    Currently about 1700 sites have chosen to enable this, although it is believed that there are many times as many actual sites currently active.

#### Is registration required ?

No. Registration is 'not' enabled by default. You need to take action to enable registering your system.

#### If I register, what is my data used for ?

Registering your station adds you to the WeeWx [station map](http://www.weewx.com/stations.html), which also makes your publically-available station_url visible so others can find your public website via the map.

It also uploads certain minimal information about your system, which is used by the core developers to better understand the usage patterns and configurations of the 'registered' user community as a whole:

* which driver is being used
* which weewx version is installed
* which python version is being used
* what kind of platform is weewx running on

We use this information to answer questions like "how many sites use this driver" and "how many sites are on the current weewx version" and the like, and to consider which configurations are used so much (or little) to merit higher or lower support levels.

#### How do I enable registering my station ?

See the pertinent section of the [User's Guide](http://www.weewx.com/docs/usersguide.htm#station_registry)

#### Can I register without specifying an Internet-facing website ?

Somewhat.  While you have to specify 'something' for the station_url in weewx.conf, it does not need to actually resolve to a real URL on a server.  This will result in you providing your forensics data (thank you) and put your station on the map, but the link 'to' your station on the map will be broken.

#### Can I unregister ?

Yes.  Simply disable your registration setting in weewx.conf and restart WeeWX.   Systems that don't periodically refresh their data are dropped from the map in about a month.
