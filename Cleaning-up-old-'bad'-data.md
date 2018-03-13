Many weather hardware will occasionally emit a bad data value. This will not only cause your plots to look bad, but it can also cause many errors in historical hi/low, NOAA reports, etc.

Here's a method for deleting these bad values in a sqlite database. You will need the command line utility `sqlite3`. To install it on a Debian system:

    sudo apt-get install sqlite3

As always, it is prudent to work off a temporary copy of your db.  Stop weewx.  Copy your database to `/var/tmp` or another scratch location.  Work on it there.  Copy the modified version back into place (possibly saving the original one just in case).

For the sake of a concrete example, let's assume that you are trying to find and remove some `windSpeed` and `windGust` values greater than 100. You have copied your SQLITE database to `/var/tmp/backup.sdb`.

To find the bad records records:
~~~~~
echo "SELECT * FROM archive WHERE (windSpeed > 100);" | sqlite3 /var/tmp/backup.sdb
echo "SELECT * FROM archive WHERE (windGust  > 100);" | sqlite3 /var/tmp/backup.sdb
~~~~~

To null out the bad fields:
~~~~~
echo "UPDATE archive SET windSpeed=NULL WHERE (windSpeed > 100);" | sqlite3 /var/tmp/backup.sdb
echo "UPDATE archive SET windGust=NULL  WHERE (windGust  > 100);" | sqlite3 /var/tmp/backup.sdb
~~~~~

Now save your old SQLITE database, then replace it with `/var/tmp/backup.sdb`.

Finally, you need to drop the old daily summaries, which otherwise would include these bad data. Use the tool [`wee_database`](http://www.weewx.com/docs/utilities.htm#wee_database_utility) to do this:

    wee_database weewx.conf --drop-daily

The summaries will be rebuilt the next time weewx starts. As this can take a long time, you
may want to do this explicitly:

    wee_database weewx.conf --rebuild-daily

Finally, to avoid a problem in the future, be sure to set a range of acceptable values for service `StdQC`. See the section [StdQC](http://weewx.com/docs/usersguide.htm#StdQC) in the User's Guide for details on how to do this.
