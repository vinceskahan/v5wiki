The LaCrosse 2300 series has a nasty bug where it seems to read 114mph when it (too frequently) loses wireless synch with the outside sensors.  This causes many errors in historical hi/low, noaa reports, etc.

To find these records:
~~~~~
echo "SELECT * FROM archive WHERE (windSpeed > 112);" | sqlite3 mydb.sdb
echo "SELECT * FROM archive WHERE (windGust  > 112);" | sqlite3 mydb.sdb
~~~~~

To null out these fields:
~~~~~
echo "UPDATE archive SET windSpeed=0 WHERE (windSpeed > 112);" | sqlite3 mydb.sdb
echo "UPDATE archive SET windGust=0  WHERE (windGust  > 112);" | sqlite3 mydb.sdb
~~~~~

As always, it is prudent to work off a temporary copy of your db.  Stop weewx.  Copy your database to `/var/tmp` or another scratch location.  Work on it there.  Copy the modified version back into place (possibly saving the original one just in case).

A better workaround is to use the new `StdQC` service and simply not save those fields that are obviously impossible in your location.
