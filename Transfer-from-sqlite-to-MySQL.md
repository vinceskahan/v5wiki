This article is about moving data from sqlite to MySQL. For information on moving the other direction, see its companion article, [Transfer from MySQL to sqlite](Transfer%20from%20MySQL%20to%20sqlite).

There are two general approaches to this problem:

1. Use the command line utility `sqlite3` to dump the contents of the sqlite database to a file, then massage the file, then upload its contents to MySQL using the utility `mysql`; or
+ Use the experimental weewx utility `transfer_db.py`.

The former is more general and can be done standalone. The latter is simpler, but requires access to the rest of weewx.

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


***************
Using transfer_db.py
-------------

For the second method, download the program `transfer_db.py` from the code repository on GitHub:

    $ wget -O transfer_db.py https://raw.githubusercontent.com/weewx/weewx/master/experimental/transfer_db.py

Open up the file in a text editor. At the top, you'll see Python dictionaries controlling the source and destination databases. The default setup is to transfer from sqlite to MySQL, which is what you want. Double check to make sure the source and destinations are to your liking. In particular, check the path to your sqlite file, `weewx.sdb`. Yours may be in a different location.

~~~~~~
old_archive_dict = {'driver'        : 'weedb.sqlite',
                    'database_name' : '/home/weewx/archive/weewx.sdb'}

new_archive_dict = {'driver'        : 'weedb.mysql',
                    'database_name' : 'weewx',
                    'host'          : 'localhost',
                    'user'          : 'weewx',
                    'password'      : 'weewx'}
~~~~~~

In order to run the program, you'll have to supply a path to the weewx executables as the variable `PYTHONPATH`. For a setup.py installation, this would be:

    $ PYTHONPATH=/home/weewx/bin python transfer_db.py

For a Debian install

    $ PYTHONPATH=/usr/share/weewx python transfer_db.py
