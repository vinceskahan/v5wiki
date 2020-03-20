This article is about moving data from sqlite to MySQL. For information on moving the other direction, see its companion article, [Transfer from MySQL to sqlite](Transfer%20from%20MySQL%20to%20sqlite).

There are two general approaches to this problem:

1. Use the command line utility `sqlite3` to dump the contents of the sqlite database to a file, then massage the file, then upload its contents to MySQL using the utility `mysql`; or
2. Use the `wee_database` utility to transfer the sqlite archive to a MySQL database.

The first is more general and can be done independently of WeeWX. The second is simpler, but requires access to a WeeWX installation.



# Using sqlite3 and mysql commands

For approach #1, first dump the sqlite database using the utility `sqlite3`. Adjust the path to the sqlite3 database, `weewx.sdb`, as necessary. Note: you may have to install `sqlite3` first. 

    $ echo ".dump archive" | sqlite3 weewx.sdb > weewx.sql

Now recreate in MySQL. Start by creating the database:

    $ echo "CREATE DATABASE weewx" | mysql -u root -p

Now restore using the contents of the file `weewx.sql`, massaged a bit:

~~~~
$ sed -e '/PRAGMA/d' -e 's/BEGIN/START/' -e 's/"archive"/archive/' < weewx.sql | mysql -u root -p --database=weewx
~~~~

This will result in a database `weewx` in MySQL, with a single table, `archive`, holding the contents of your sqlite archive file.

If the new database is to be used as the WeeWX archive, WeeWX will need to be configured to use MySQL as per the [Configuring MySQL](http://weewx.com/docs/usersguide.htm#configuring_mysql) section of the [User's Guide](http://weewx.com/docs/usersguide.htm).

There is one more step before the new database can be used with WeeWX: creating the daily summaries inside the new database. If WeeWX is configured to use the new database the daily summaries will be built automatically at the next startup. Alternatively, the daily summaries can be built manually using the `wee_database` utility and the `--rebuild-daily` option:

    $ wee_database --rebuild-daily


# Using wee_database

To transfer a database using `wee_database`, data bindings for both the source and destination databases must exist in `weewx.conf`. Because the source database most likely will already exist, in most cases, only a binding for the destination database will need to be added to `weewx.conf`. To do this, edit `weewx.conf`, locate the `[DataBinding]` section, and add a binding for the destination database similar to the following:

        [[dest_binding]]
            database = archive_mysql
            table_name = archive
            manager = weewx.wxmanager.WXDaySummaryManager
            schema = schemas.wview.schema

    
The above settings add a database binding `dest_binding` to a database entry `archive_mysql` in the `weewx.conf [Databases]` section, and use the default `wview` database schema. If this is not the case (e.g., you use a modified schema) you will need to alter one of more of the above settings accordingly.

Before doing the transfer, it may worthwhile to run `wee_database` with the `--help` option:

    $ wee_database --help

Note the `--dry-run` option. When used with the `--transfer` option, `wee_database` prints out what would happen but does not actually do the transfer.

To use the `--dry-run` option with `--transfer`:

    $ wee_database --transfer --dest-binding=dest_binding --dry-run

This will result in output something like this:

    Using configuration file /home/weewx/weewx.conf
    Using database binding 'wx_binding', which is bound to database 'archive_sqlite'
    Transfer 7814 records from source database 'weewx.sdb' to destination database 'weewx'.
    Dry run, nothing done.

If the dry run was successful the transfer can be completed using the following command:

    $ wee_database --transfer --dest-binding=dest_binding

This will result in output something like this:

    Using configuration file /home/weewx/weewx.conf
    Using database binding 'wx_binding', which is bound to database 'archive_sqlite'
    Transfer 7814 records from source database 'weewx.sdb' to destination database 'weewx' (y/n)? y
    transferring, this may take a while.... complete
    7814 records transferred from source database 'weewx.sdb' to destination database 'weewx'.

Once the transfer is complete and before the new database can be used with WeeWX the daily summaries need to be created inside the new database. If WeeWX is configured to use the new database, the daily summaries will be built automatically by WeeWX at the next startup. Alternatively, the daily summaries can be built manually using the `wee_database` utility and the `--rebuild-daily` option:

    $ wee_database --rebuild-daily

This rebuilds the daily summaries of the database referred to by the binding `db_binding` in `weewx.conf` (or the `wee_database` option `--binding` if used) from the archive data in the same database.

If the new database is to be used as the WeeWX archive then WeeWX needs to be configured to use MySQL as per the [Configuring MySQL](http://weewx.com/docs/usersguide.htm#configuring_mysql) section of the [User's Guide](http://weewx.com/docs/usersguide.htm)


# Caveats

WeeWX v3.0.0 or greater is required in order to use `wee_database` to transfer from sqlite to MySQL. When weeWX is installed using setup.py and the default location of `/home/weewx`, `wee_database` is installed to `/home/weewx/bin/wee_database`. When weeWX is installed using .deb or .rpm, `wee_database` is installed to `/usr/share/weewx/wee_database` with a symbolic link `/usr/bin/wee_database`.