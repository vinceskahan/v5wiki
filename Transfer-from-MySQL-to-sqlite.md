This article covers moving from MySQL to sqlite. It's from an [email exchange](https://groups.google.com/d/msg/weewx-user/kwkyAebHyXI/vdEJQdfEG9QJ) with user Roel. For information on moving the other direction, see its companion article, [Transfer from sqlite to MySQL](Transfer%20from%20sqlite%20to%20MySQL/).

There are two general approaches to this problem:

1. Use `mysqldump` to create a dump file, massage it, then restore using the utility `sqlite3`; or
+ Use the experimental weewx utility `transfer_db.py`.

The former is more general and can be done standalone. The latter is simpler, but requires access to the rest of weeWX.

*Please note that the `python-mysqldb` module is required to use weeWX with a MySQL database.*

******************
Dumping to SQL
-------------

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
            # The database must match one of the sections in [Databases].
            database = archive_sqlite
            # The name of the table within the database
            table_name = archive
            # The manager handles aggregation of data for historical summaries
            manager = weewx.wxmanager.WXDaySummaryManager
            # The schema defines the structure of the database.
            # It is *only* used when the database is created.
            schema = schemas.wview.schema

Note the above settings are based on there being an `archive_sqlite` entry in the `weewx.conf [Databases]` section and the use of the standard weewx database schema. If this is not the case (eg you use a modified schema) you will need to alter one of more of the above settings accordingly.

Before the new database can be used with weewx the daily summaries need to be created inside the new database. If weewx is configured to use the new database the daily summaries will be built automatically by weewx at the next startup. Alternatively, the daily summaries can be built manually using the `wee_database` utility and the `--backfill-daily` option:

    $ wee_database --backfill-daily

Before the new settings will take effect the weewx config settings will need to be reloaded or weewx stopped then started:

- config reload:

        $ sudo service weewx reload

    or

        $ sudo /etc/init.d/weewx reload

- stop then start:

        $ sudo service weewx stop
        $ sudo service weewx start

    or

        $ sudo /etc/init.d/weewx stop
        $ sudo /etc/init.d/weewx start

***************
Using wee_database
-------------

Weewx v3.0.0 or greater is required in order to use `wee_database` to transfer from MySQL to sqlite. When weewx is installed using setup.py and the default location of `/home/weewx`, `wee_database` is installed to `/home/weewx/bin/wee_database`. When weewx is installed using .deb or .rpm, `wee_database` is installed to `/usr/share/weewx/wee_database` with a symbolic link `/usr/bin/wee_database`.

The version of `wee_database` that includes the database transfer option was not included with weewx v3.2.1 and earlier. In this case, the latest version of `wee_database` can be downloaded from the weewx GitHub code repository:

    $ wget -O wee_datbase https://raw.githubusercontent.com/weewx/weewx/master/bin/wee_database

In order to use `wee_database` to transfer a database, data bindings for both the source and destination databases must exist in `weewx.conf`. As the source database will most likely be the archive database currently used by weewx, it is likely that only the destination database will require the addition of a data binding to `weewx.conf`. To add a data binding; edit `weewx.conf`, locate the `[DataBinding]` section and add a binding similar to the following:

        [[dest_binding]]
            # The database must match one of the sections in [Databases].
            database = archive_sqlite
            # The name of the table within the database
            table_name = archive
            # The manager handles aggregation of data for historical summaries
            manager = weewx.wxmanager.WXDaySummaryManager
            # The schema defines the structure of the database.
            # It is *only* used when the database is created.
            schema = schemas.wview.schema

    
The above settings are based on there being an `archive_sqlite` entry in the `weewx.conf [Databases]` section and the use of the standard weewx database schema. If this is not the case (eg you use a modified schema) you will need to alter one of more of the above settings accordingly.

Once the necessary changes to `weewx.conf` have been made and `weewx.conf` has been saved, the `wee_database` utility may be run with the `--transfer` option. Before running `wee_database` with the `--transfer` option it may worthwhile running `wee_database` with the `--help` option as follows:

    $ wee_database --help

This will result in an output that looks something like this:

    Usage: wee_database --help
           wee_database --create-archive
                [CONFIG_FILE|--config=CONFIG_FILE]
                [--binding=BINDING_NAME]
           wee_database --drop-daily
                [CONFIG_FILE|--config=CONFIG_FILE]
                [--binding=BINDING_NAME]
           wee_database --backfill-daily
                [CONFIG_FILE|--config=CONFIG_FILE]
                [--binding=BINDING_NAME]
           wee_database --reconfigure
                [CONFIG_FILE|--config=CONFIG_FILE]
                [--binding=BINDING_NAME]
           wee_database --string-check
                [CONFIG_FILE|--config=CONFIG_FILE]
                [--binding=BINDING_NAME] [--fix]
           wee_database --transfer
                [CONFIG_FILE|--config=CONFIG_FILE]
                [--binding=BINDING_NAME]
                --dest-binding=BINDING_NAME
                [--dry-run]
    
    
    Configure the weewx databases. Most of these functions are handled
    automatically by weewx, but they may be useful as a utility in special cases.
    In particular, the 'reconfigure' option can be useful if you decide to add or
    drop data types from the database schema or change unit systems and the
    'transfer' option is useful if converting from one database type to another.
    
    Options:
      -h, --help            show this help message and exit
      --config=CONFIG_FILE  Use configuration file CONFIG_FILE.
      --create-archive      Create the archive database.
      --drop-daily          Drop the daily summary tables from a database.
      --backfill-daily      Backfill a database with daily summaries.
      --reconfigure         Create a new archive database using configuration
                            information found in the configuration file. In
                            particular, the new database will use the unit system
                            found in option [StdConvert][target_unit]. The new
                            database will have the same name as the old database,
                            with a '_new' on the end.
      --string-check        Check a sqlite version of the archive database to see
                            whether it contains embedded strings.
      --fix                 Fix any embedded strings in a sqlite database.
      --transfer            Transfer the weewx archive from source database to
                            destination database.
      --binding=BINDING_NAME
                            The data binding. Default is 'wx_binding'.
      --dest-binding=BINDING_NAME
                            The destination data binding.
      --dry-run             Print what would happen but do not do it.
    
    If you are using a MySQL database it is assumed that you have the appropriate
    permissions for the requested operation.

Note the `--dry-run` option when used with the `--transfer` option prints out what would happen but does not actually do the transfer. This is useful if this is a first time transfer or to confirm the transfer settings.

To use the `--dry-run` option with `--transfer`:

    $ wee_database --transfer --dest-binding=dest_binding --dry-run

This will result in an output that looks something like this:

    Using configuration file /home/weewx/weewx.conf
    Using database binding 'wx_binding', which is bound to database 'archive_mysql'
    Transfer 7814 records from source database 'weewx' to destination database 'weewx.sdb'.
    Dry run, nothing done.

If the dry run was successful the transfer can be completed using the following command:

    $ wee_database --transfer --dest-binding=dest_binding

This will result in an output that looks something like this:

    Using configuration file /home/weewx/weewx.conf
    Using database binding 'wx_binding', which is bound to database 'archive_mysql'
    Transfer 7814 records from source database 'weewx' to destination database 'weewx.sdb' (y/n)? y
    transferring, this may take a while.... complete
    7814 records transferred from source database 'weewx' to destination database 'weewx.sdb'.

Once the transfer is complete and before the new database can be used with weewx the daily summaries need to be created inside the new database. If weewx is configured to use the new database the daily summaries will be built automatically by weewx at the next startup. Alternatively, the daily summaries can be built manually using the `wee_database` utility and the `--backfill-daily` option:

    $ wee_database --backfill-daily --binding=dest_binding

If the new database is to be used as the weewx archive then weewx needs to be configured to use sqlite for the weewx archive. This can be done by either changing the `[StdArchive] wx_binding` setting in `weewx.conf` or by changing the settings of the data binding to which `[StdArchive] data_binding` refers. 
    
- To change the `[StdArchive] wx_binding` setting edit `weewx.conf` and change the `[StdArchive] data_binding` setting to use the `--dest_binding` used during the `wee_database` transfer eg:

        [StdArchive]
        
            .....
            data_binding = dest_binding 

- To change the settings of the data binding referred to by `[StdArchive] data_binding`, edit `weewx.conf` and if the data binding to which `[StdArchive] data_binding` refers to is `wx_binding`, change the `[DataBindings] wx_binding` settings as follows:

        [DataBindings]
        
            [[wx_binding]]
                # The database must match one of the sections in [Databases].
                database = archive_sqlite
                # The name of the table within the database
                table_name = archive
                # The manager handles aggregation of data for historical summaries
                manager = weewx.wxmanager.WXDaySummaryManager
                # The schema defines the structure of the database.
                # It is *only* used when the database is created.
                schema = schemas.wview.schema

    Note the above settings are based on there being an `archive_sqlite` entry in the `weewx.conf [Databases]` section and the use of the standard weewx database schema. If this is not the case (eg you use a modified schema) you will need to alter one of more of the above settings accordingly.

If `weewx.conf` was changed the weewx config settings will need to be reloaded or weewx stopped then started before the new settings take effect:

- config reload:

        $ sudo service weewx reload

    or

        $ sudo /etc/init.d/weewx reload

- stop then start:

        $ sudo service weewx stop
        $ sudo service weewx start

    or

        $ sudo /etc/init.d/weewx stop
        $ sudo /etc/init.d/weewx start