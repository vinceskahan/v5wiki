This article covers moving from MySQL to sqlite. It's from an [email exchange](https://groups.google.com/d/msg/weewx-user/kwkyAebHyXI/vdEJQdfEG9QJ) with user Roel. For information on moving the other direction, see its companion article, [Transfer from sqlite to MySQL](Transfer%20from%20sqlite%20to%20MySQL/).

There are two general approaches to this problem:

1. Use mysqldump to create a dump file, massage it, then restore using the utility sqlite3; or
+ Use the experimental weewx utility transfer_db.py

The former is more general and can be done standalone. The latter is simpler, but requires access to the rest of weewx.
******************
Dumping to SQL
-------------

For approach #1, first dump the MySQL database using mysqldump.

    $ mysqldump -u root -p --skip-add-drop-table --skip-add-locks --extended-insert=FALSE weewx archive > weewx.sql

Unfortunately, the resultant file includes a CREATE TABLE statement that sqlite does not understand. I could write some fancy sed script here to massage it into something sqlite3 can deal with, but the simplest is to just edit the file weewx.sql, replacing the CREATE TABLE statement with:

~~~~~~
CREATE TABLE archive (`dateTime` INTEGER NOT NULL UNIQUE PRIMARY KEY, 
`usUnits` INTEGER NOT NULL, `interval` INTEGER NOT NULL, 
`barometer` REAL, `pressure` REAL, `altimeter` REAL, `inTemp` REAL,
`outTemp` REAL, `inHumidity` REAL, `outHumidity` REAL, `windSpeed` REAL,
`windDir` REAL, `windGust` REAL, `windGustDir` REAL, `rainRate` REAL,
`rain` REAL, `dewpoint` REAL, `windchill` REAL, `heatindex` REAL, 
`ET` REAL, `radiation` REAL, `UV` REAL, `extraTemp1` REAL, 
`extraTemp2` REAL, `extraTemp3` REAL, `soilTemp1` REAL, 
`soilTemp2` REAL, `soilTemp3` REAL, `soilTemp4` REAL, 
`leafTemp1` REAL, `leafTemp2` REAL, `extraHumid1` REAL, 
`extraHumid2` REAL, `soilMoist1` REAL, `soilMoist2` REAL, 
`soilMoist3` REAL, `soilMoist4` REAL, `leafWet1` REAL, `leafWet2` REAL, 
`rxCheckPercent` REAL, `txBatteryStatus` REAL, 
`consBatteryVoltage` REAL, `hail` REAL, `hailRate` REAL, 
`heatingTemp` REAL, `heatingVoltage` REAL, `supplyVoltage` REAL, 
`referenceVoltage` REAL, `windBatteryStatus` REAL, 
`rainBatteryStatus` REAL, `outTempBatteryStatus` REAL, 
`inTempBatteryStatus` REAL);
~~~~~~

Then restore using sqlite3 (which you may have to install first)

    $ sqlite3 weewx.sdb < weewx.sql 

Put the resultant sqlite3 file, weewx.sdb, wherever your installation method requires.

***************
Using transfer_db.py
-------------

For the second method, download the program transfer_db.py from the code repository on SourceForge:

    $ wget -O transfer_db.py http://sourceforge.net/p/weewx/code/HEAD/tree/trunk/experimental/transfer_db.py?format=raw

Open up the file in a text editor. At the top, you'll see Python dictionaries controlling the source and destination databases. The default setup is to transfer from sqlite to MySQL, so you'll want to swap the two dictionaries to go the other direction. When you're done, they'll look like  this:

~~~~~~
new_archive_dict = {'driver'        : 'weedb.sqlite',
                    'database_name' : '/home/weewx/archive/weewx.sdb'}

old_archive_dict = {'driver'        : 'weedb.mysql',
                    'database_name' : 'weewx',
                    'host'          : 'localhost',
                    'user'          : 'weewx',
                    'password'      : 'weewx'}
~~~~~~

(You may also want to adjust the path to the resultant sqlite database to something other than '/home/weewx/archive/weewx.sdb')

In order to run the program, you'll have to supply a path to the weewx executables as variable PYTHONPATH. For a setup.py installation, this would be:

    $ PYTHONPATH=/home/weewx/bin python transfer_db.py

For a Debian install

    $ PYTHONPATH=/usr/share/weewx/bin python transfer_db.py