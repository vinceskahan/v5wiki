This article is about moving data from sqlite to MySQL. For information on moving the other direction, see its companion article, [Transfer from MySQL to sqlite](Transfer%20from%20MySQL%20to%20sqlite).

There are two general approaches to this problem:

1. Use the command line utility `sqlite3` to dump the contents of the sqlite database to a file, then massage the file, then upload its contents to MySQL using the utility `mysql`; or
2. Use the `weectl database` utility to transfer the sqlite archive to a MySQL database (Version 5 only).

The first is more general and can be done independently of WeeWX. The second is simpler, but requires access to a WeeWX installation. It also tends to be faster because the entire transfer is done as a single transaction.



# Using `sqlite3` and `mysql` commands

For approach #1, first dump the sqlite database using the utility `sqlite3`. Adjust the path to the sqlite3 database, `weewx.sdb`, as necessary. Note: you may have to install `sqlite3` first. 

    $ echo ".dump archive" | sqlite3 weewx.sdb > weewx.sql

Now recreate in MySQL. Start by creating the database:

    $ echo "CREATE DATABASE weewx" | mysql -u root -p

Now restore using the contents of the file `weewx.sql`, massaged a bit:

~~~~
$ sed -e '/PRAGMA/d' -e 's/BEGIN/START/' < weewx.sql | mysql -u root -p --database=weewx
~~~~

This will result in a database `weewx` in MySQL, with a single table, `archive`, holding the contents of your sqlite archive file.

If the new database is to be used as the WeeWX archive, WeeWX will need to be
configured to use MySQL as per the section [_Configuring MySQL /
MariaDB_](https://www.weewx.com/docs/5.0/usersguide/mysql-mariadb/) in the
User's Guide.

There is one more step before the new database can be used with WeeWX: creating
the daily summaries inside the new database. If WeeWX is configured to use the
new database the daily summaries will be built automatically at the next
startup. Alternatively, the daily summaries can be built manually:

    $ weectl database rebuild-daily


# Using `weectl database`

Using the utility `weectl database` is often faster. My modest Intel NUC, using
a solid-state disk, can process about 1,500 records per second.

To transfer a database using `weectl database`, data bindings for both the
source and destination databases must exist in `weewx.conf`. Because the source
database most likely will already exist, in most cases, only a binding for the
destination database will need to be added to `weewx.conf`. To do this, edit
`weewx.conf`, locate the `[DataBinding]` section, and add a binding for the
destination database similar to the following:

    [[dest_binding]]
        database = archive_mysql
        table_name = archive
        manager = weewx.manager.DaySummaryManager
        schema = schemas.wview_extended.schema

    
The above settings adds a new database binding `dest_binding`. It will use the database `archive_mysql` in the `[Databases]` section of `weewx.conf`, and the `wview_extended` database schema. If this is not the case (_e.g._, you use a modified schema) you will need to alter one of more of the above settings accordingly.

Before doing the transfer, it may worthwhile to run `weectl database transfer` with the `--help` option:

    $ weectl database transfer --help

Note the `--dry-run` option. When used with the `transfer` action, `weectl database` prints out what would happen but does not actually do the transfer.

    $ weectl database transfer --dest-binding=dest_binding --dry-run

This will result in output something like this:

```
Using configuration file /Users/user-name/weewx-data/weewx.conf
This is a dry run. Nothing will actually be done.
Transfer 3654 records from source database 'weewx.sdb' to destination database 'weewx' (y/n)? y
Transferring, this may take a while.... 

Completed.
0 records transferred from source database 'weewx.sdb' to destination database 'weewx' in 0.09 seconds.
This was a dry run. Nothing was actually done.
```

If the dry run was successful the transfer can be completed using the following command:

    $ weectl database transfer --dest-binding=dest_binding

This will result in output something like this:

```
Using configuration file /Users/user-name/weewx-data/weewx.conf
Transfer 3654 records from source database 'weewx.sdb' to destination database 'weewx' (y/n)? y
Transferring, this may take a while.... 
Records processed: 3000; time: 2022-07-31 00:31:00 PDT (1659252660)
Completed.
3654 records transferred from source database 'weewx.sdb' to destination database 'weewx' in 0.93 seconds.
```

Once the transfer is complete and before the new database can be used with WeeWX
the daily summaries need to be created inside the new database. If WeeWX is
configured to use the new database, the daily summaries will be built
automatically by WeeWX at the next startup. Alternatively, the daily summaries
can be built manually using the `weectl database` utility:

    $ weectl database rebuild-daily --binding=dest_binding

This rebuilds the daily summaries of the database referred to by the binding
`dest_binding` in `weewx.conf`.

If the new database is to be used as the WeeWX archive then WeeWX needs to be
configured to use MySQL as per the [_Configuring MySQL /
MariaDB_](https://www.weewx.com/docs/5.0/usersguide/mysql-mariadb/) in the
User's Guide.
