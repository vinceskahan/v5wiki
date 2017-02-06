This article covers moving from MySQL to sqlite. It's from an [email exchange](https://groups.google.com/d/msg/weewx-user/kwkyAebHyXI/vdEJQdfEG9QJ) with user Roel. For information on moving the other direction, see its companion article, [Transfer from sqlite to MySQL](Transfer%20from%20sqlite%20to%20MySQL/).

There are two general approaches to this problem:

1. Use `mysqldump` to create a dump file, massage it, then restore using the utility `sqlite3`; or
+ Use the `wee_database` utility.

The former is more general and can be done independently of weeWX. The latter is simpler, but requires access to the rest of weeWX.

*Please note that the `python-mysqldb` module is required to use weeWX with a MySQL database.*


# Dumping to SQL

For approach #1, first dump the MySQL database using `mysqldump`.

    $ mysqldump -u root -p --skip-add-drop-table --skip-add-locks --extended-insert=FALSE weewx archive > weewx.sql

Unfortunately, the resultant file includes a `CREATE TABLE` statement that sqlite does not understand. I could write some fancy sed script here to massage it into something sqlite3 can deal with, but the simplest is to just edit the file `weewx.sql`, replacing the `CREATE TABLE` statement with:

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

Then restore using `sqlite3` (which you may have to install first)

    $ sqlite3 weewx.sdb < weewx.sql 

Put the resultant sqlite3 file, `weewx.sdb`, wherever your installation method requires.

If the new database is to be used as the weewx archive then weewx needs to be configured to use sqlite for the weewx archive. This can be done by changing the settings of the data binding to which `[StdArchive] data_binding` refers. To change the settings of the data binding referred to by `[StdArchive] data_binding`, edit `weewx.conf` and if the data binding to which `[StdArchive] data_binding` refers to is `wx_binding`, change the `[DataBindings] wx_binding` settings as follows:

    [DataBindings]
    
        [[wx_binding]]
            database = archive_sqlite
            table_name = archive
            manager = weewx.wxmanager.WXDaySummaryManager
            schema = schemas.wview.schema

Note the above settings are based on there being an `archive_sqlite` entry in the `weewx.conf [Databases]` section and the use of the standard weewx database schema. If this is not the case (eg you use a modified schema) you will need to alter one of more of the above settings accordingly.


# Using wee_database

To transfer a database using `wee_database`, data bindings for both the source and destination databases must exist in `weewx.conf`. As the source database will most likely be the archive database currently used by weeWX, it is likely that only the destination database will require the addition of a data binding to `weewx.conf`. To add a data binding; edit `weewx.conf`, locate the `[DataBinding]` section and add a binding similar to the following:

        [[dest_binding]]
            database = archive_sqlite
            table_name = archive
            manager = weewx.wxmanager.WXDaySummaryManager
            schema = schemas.wview.schema

The above settings assume an `archive_sqlite` entry in the `weewx.conf [Databases]` section and use the default `wview` database schema. If this is not the case (e.g., you use a modified schema) you will need to alter one of more of the above settings accordingly.

Before doing the transfer, it may worthwhile to run `wee_database` with the `--help` option:

    $ wee_database --help

Note the `--dry-run` option. When used with the `--transfer` option, `wee_database` prints out what would happen but does not actually do the transfer.

To use the `--dry-run` option with `--transfer`:

    $ wee_database --transfer --dest-binding=dest_binding --dry-run

This will result in output something like this:

    Using configuration file /home/weewx/weewx.conf
    Using database binding 'wx_binding', which is bound to database 'archive_mysql'
    Transfer 7814 records from source database 'weewx' to destination database 'weewx.sdb'.
    Dry run, nothing done.

If the dry run was successful the transfer can be completed using the following command:

    $ wee_database --transfer --dest-binding=dest_binding

This will result in output something like this:

    Using configuration file /home/weewx/weewx.conf
    Using database binding 'wx_binding', which is bound to database 'archive_mysql'
    Transfer 7814 records from source database 'weewx' to destination database 'weewx.sdb' (y/n)? y
    transferring, this may take a while.... complete
    7814 records transferred from source database 'weewx' to destination database 'weewx.sdb'.

Once the transfer is complete and before the new database can be used with weeWX the daily summaries need to be created inside the new database. If weeWX is configured to use the new database, the daily summaries will be built automatically by weeWX at the next startup. Alternatively, the daily summaries can be built manually using the `wee_database` utility and the `--backfill-daily` option:

    $ wee_database --backfill-daily --binding=dest_binding

The last step is to configure weeWX to use the sqlite database.  In `weewx.conf`, modify the `wx_binding` to match the `dest_binding`:

        [DataBindings]
            [[wx_binding]]
                database = archive_sqlite
                table_name = archive
                manager = weewx.wxmanager.WXDaySummaryManager
                schema = schemas.wview.schema

Be sure to stop then start weeWX after any changes to `weewx.conf`.


# Caveats

Weewx v3.0.0 or greater is required in order to use `wee_database` to transfer from MySQL to sqlite. When weeWX is installed using setup.py and the default location of `/home/weewx`, `wee_database` is installed to `/home/weewx/bin/wee_database`. When weeWX is installed using .deb or .rpm, `wee_database` is installed to `/usr/share/weewx/wee_database` with a symbolic link `/usr/bin/wee_database`.

The database transfer option was added in v3.3.0.  For weeWX v3.2.1 and earlier, the latest version of `wee_database` can be downloaded from the weewx GitHub code repository:

    $ wget -O wee_datbase https://raw.githubusercontent.com/weewx/weewx/master/bin/wee_database
