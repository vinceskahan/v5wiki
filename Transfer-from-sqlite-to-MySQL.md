This article is about moving data from sqlite to MySQL. For information on moving the other direction, see its companion article, [Transfer from MySQL to sqlite](Transfer%20from%20MySQL%20to%20sqlite).

There are two general approaches to this problem:

1. Use the command line utility `sqlite3` to dump the contents of the sqlite database to a file, then massage the file, then upload its contents to MySQL using the utility `mysql`; or
+ Use the `wee_database` utility to transfer the sqlite archive to a MySQL database.

The first is more general and can be done standalone. The second is simpler, but requires access to the rest of weewx.

*Please note that the `python-mysqldb` module is required to use weeWX with a MySQL database.*

******************
Dumping to SQL
-------------

For approach #1, first dump the sqlite database using the utility `sqlite3`. Adjust the path to the sqlite3 database, `weewx.sdb`, as necessary. Note: you may have to install `sqlite3` first. 

    $ echo ".dump archive" | sqlite3 weewx.sdb > weewx.sql

Now recreate in MySQL. Start by creating the database:

    $ echo "CREATE DATABASE weewx" | mysql -u root -p

Now restore using the contents of the file `weewx.sql`, massaged a bit:

~~~~
$ sed -e '/PRAGMA/d' -e's/BEGIN/START/' -e 's/"archive"/archive/' < weewx.sql | mysql -u root -p --database=weewx
~~~~

This will result in a database `weewx` in MySQL, with a single table, `archive`, holding the contents of your sqlite archive file.

If the new database is to be used as the weewx archive, weewx will need to be configured to use MySQL as per the [Configuring MySQL](http://weewx.com/docs/usersguide.htm#configuring_mysql) section of the [User's Guide](http://weewx.com/docs/usersguide.htm).

There is one more step before the new database can be used with weewx: creating the daily summaries inside the new database. If weewx is configured to use the new database the daily summaries will be built automatically by weewx at the next startup. Alternatively, the daily summaries can be built manually using the `wee_database` utility and the `--backfill-daily` option:

    $ wee_database --backfill-daily

***************
Using wee_database
-------------

Weewx v3.0.0 or greater is required in order to use `wee_database` to transfer from sqlite to MySql. When weewx is installed using setup.py and the default location of `/home/weewx`, `wee_database` is installed to `/home/weewx/bin/wee_database`. When weewx is installed using .deb or .rpm, `wee_database` is installed to `/usr/share/weewx/wee_database` with a symbolic link `/usr/bin/wee_database`.

The version of `wee_database` that includes the database transfer option was not included with weewx v3.2.1 and earlier. In this case, the latest version of `wee_database` can be downloaded from the weewx GitHub code repository:

    $ wget -O wee_database https://raw.githubusercontent.com/weewx/weewx/master/bin/wee_database

In order to use `wee_database` to transfer a database, data bindings for both the source and destination databases must exist in `weewx.conf`. As the source database will most likely be the archive database currently used by weewx, it is likely that only the destination database will require the addition of a data binding to `weewx.conf`. To add a data binding; edit `weewx.conf`, locate the `[DataBinding]` section and add a binding similar to the following:

        [[dest_binding]]
            # The database must match one of the sections in [Databases].
            database = archive_mysql
            # The name of the table within the database
            table_name = archive
            # The manager handles aggregation of data for historical summaries
            manager = weewx.wxmanager.WXDaySummaryManager
            # The schema defines the structure of the database.
            # It is *only* used when the database is created.
            schema = schemas.wview.schema

    
The above settings are based on there being an `archive_mysql` entry in the `weewx.conf [Databases]` section and the use of the standard weewx database schema. If this is not the case (eg you use a modified schema) you will need to alter one of more of the above settings accordingly.

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
    Using database binding 'wx_binding', which is bound to database 'archive_sqlite'
    Transfer 7814 records from source database 'weewx.sdb' to destination database 'weewx'.
    Dry run, nothing done.

If the dry run was successful the transfer can be completed using the following command:

    $ wee_database --transfer --dest-binding=dest_binding

This will result in an output that looks something like this:

    Using configuration file /home/weewx/weewx.conf
    Using database binding 'wx_binding', which is bound to database 'archive_sqlite'
    Transfer 7814 records from source database 'weewx.sdb' to destination database 'weewx' (y/n)? y
    transferring, this may take a while.... complete
    7814 records transferred from source database 'weewx.sdb' to destination database 'weewx'.

Once the transfer has been completed there is one more step before the new database can be used with weewx: creating the daily summaries inside the new database. If weewx is configured to use the new database the daily summaries will be built automatically by weewx at the next startup. Alternatively, the daily summaries can be built manually using the `wee_database` utility and the `--backfill-daily` option:

    $ wee_database --backfill-daily --binding=dest_binding

If the new database is to be used as the weewx archive then weewx needs to be configured to use MySQL as per the [Configuring MySQL](http://weewx.com/docs/usersguide.htm#configuring_mysql) section of the [User's Guide](http://weewx.com/docs/usersguide.htm)