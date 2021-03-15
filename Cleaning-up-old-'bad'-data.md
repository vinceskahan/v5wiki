Many weather hardware will occasionally emit a bad data value. This will not only cause your plots to look bad, but it can also cause many errors in historical hi/low, NOAA reports, etc.

Here's a method for deleting these bad values in a sqlite database. 

## Cleaning archive data

You will need the command line utility `sqlite3`. To install it on a Debian system:

    sudo apt-get install sqlite3

As always, it is prudent to work off a temporary copy of your db.  Stop weewx.  Copy your database to `/var/tmp` or another scratch location.  Work on it there.  Copy the modified version back into place (having saved the original one just in case).

### Bad data

For the sake of a concrete example, let's assume that you are trying to find and remove some `windSpeed` and `windGust` values greater than 100. You have copied your SQLITE database to `/var/tmp/backup.sdb`.

To find the bad records records:
~~~~~
echo "SELECT * FROM archive WHERE (windSpeed > 100);" | sqlite3 /var/tmp/backup.sdb
echo "SELECT * FROM archive WHERE (windGust  > 100);" | sqlite3 /var/tmp/backup.sdb
~~~~~

Replace bad fields with the value null
~~~~~
echo "UPDATE archive SET windSpeed=NULL WHERE (windSpeed > 100);" | sqlite3 /var/tmp/backup.sdb
echo "UPDATE archive SET windGust=NULL  WHERE (windGust  > 100);" | sqlite3 /var/tmp/backup.sdb
~~~~~

Now save your old SQLITE database, then replace it with `/var/tmp/backup.sdb`.

### Deleting unwanted records

Here's another example. A common scenario is to have some unwanted data at the beginning of your database, caused by a failure of 
your computer's clock to start correctly. You want to delete these data. For purposes of this example, suppose you know that your
first good record is 12-May 2018 1120 UTC.

Use a tool like the website [epochconverter.com](https://www.epochconverter.com/) to figure out what this is in Unix epoch time. The answer for the example is `1526124000`.

Now use the tool `sqlite3` to see what records predate that. We assume that you have copied your SQLITE database to `/var/tmp/backup.sdb`.

```shell
sqlite3 /var/tmp/backup.sdb
sqlite> select datetime(dateTime, 'unixepoch', 'localtime'), dateTime from archive where dateTime < 1526124000;
```

If it's all garbage, then it's safe to delete these data:

```shell
sqlite> delete from archive where dateTime <1526124000;
sqlite> .quit
```

Now save your old SQLITE database, then replace it with `/var/tmp/backup.sdb`.


## Daily summaries

Finally, you need to drop the old daily summaries, which otherwise would include these bad data. Use the tool [`wee_database`](http://www.weewx.com/docs/utilities.htm#wee_database_utility) to do this:

    wee_database weewx.conf --drop-daily

The summaries will be rebuilt the next time weewx starts. As this can take a long time, you
may want to do this explicitly:

    wee_database weewx.conf --rebuild-daily

## Regenerate web pages

Most old web pages and plots will be automatically regenerated, but it may take a while. The exception is
the "NOAA" reports --- only the most recent one will get regenerated. If your bad data is deeper in the
past, then you should delete the offending NOAA files and let WeeWX regenerate them.

## Preventing future problems

Finally, to avoid a problem in the future, be sure to set a range of acceptable values for service `StdQC`. See the section [StdQC](http://weewx.com/docs/usersguide.htm#StdQC) in the User's Guide for details on how to do this.
