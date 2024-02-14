## Directions

### `sqlite3`
The SQLite archive database (usually named `wview-archive.sdb`) used by the Linux weather software program wview, is completely compatible with WeeWX and can be used without change. All you need do is copy the file over and change its name. 

Assuming that the wview database is in file `/var/wview/archive/wview-archive.sdb` (its usual place),

```sh
sudo systemctl stop weewx
cd SQLITE_ROOT
mv weewx.sdb weewx.sdb.bak
cp /var/wview/archive/wview-archive.sdb weewx.sdb
sudo systemctl start weewx
```

where the variable _`SQLITE_ROOT`_ is the directory where your WeeWX SQLite database is located (usually `/home/weewx/archive` or `/var/lib/weewx`).

Internally, within the `weewx.sdb` database, WeeWX also maintains a "daily summary" of all the statistics. This is done for performance reasons. The daily summary will be built automatically on the first startup. This could take a few minutes if the wview archive contains more than a month or two of data.

On my modest 500 MHz fit-PC Slim with 512 MB of memory it took a little over 4 minutes for a year and a half (25 MB) of data.

### MySQL
The MySQL archive database used by wview is "almost" compatible with WeeWX. The one difference is that in wview, the column `interval` is named `arcInt` (presumably because `interval` is a keyword in MySQL, although it can still be used by surrounding the word with backquotes).

To change the column name to what WeeWX uses, namely `interval`, use the utility `mysql` and issue the command:

    mysql> ALTER TABLE your-wview-archive-name CHANGE arcInt `interval` INTEGER NOT NULL; 

where `your-wview-archive-name` is the name of your wview archive database. Note that the word `interval` is surrounded by backquotes.

Then in the `[Databases]` section of `weewx.conf`, replace the name of the database with whatever your installation of wview used (`your-wview-archive-name` in this example):

```ini
[[archive_mysql]]
    database_type = MySQL
    database_name = your-wview-archive-name
```

