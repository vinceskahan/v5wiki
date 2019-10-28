Say you have collected some data at a 1 minute interval, and after some months you decide that using only a 5 minute interval will be sufficient. You don't want to lose the data already collected, so this page describes how you can transform the existing data to be accepted in the new scheme.

In this example, the target interval is assumed to be 5 minutes - the code was tested for input at 1 minute intervals, but should work as-is for anything less than 5 minutes. If you want to use an interval longer than 5 minutes then you will need to adjust the code slightly.

In order to not produce any more data at the short interval, weewx should either not be running, or should have already been converted to the longer interval.

There are two main approaches that could be taken:
1. on exported data, or
1. work directly in the database software (either SQLite or MySQL)

If you are familiar working from the command line utility programs to manipulate the databases then you should be able to simply use the sql scripts below.
For those less familiar, there are many GUI options, such as:
* SQLite:  [DB Browser for SQLite](https://sqlitebrowser.org/) is one, and runs under MS-Windows, Linux and Max OS-X. Note that the SQLite client _must have the maths extensions_ to the language. The current DB browser version ships with it, but it needs to be loaded - there is a preference setting to select the extension to be automatically loaded.
* [MySQL Workbench](https://dev.mysql.com/doc/workbench/en/) if your database is hosted on a MySQL or MariaDB server. This also has multiple OS support. 

## Export, Consolidate and Import
Both of the GUIs offer ways to efficiently export and import CSV formatted files of the archive table.

If your archive contains mixed interval data, then extract the required data into a separate table
and export that.
``` SQL
CREATE  TABLE asmall AS
	SELECT * FROM
		archive
	WHERE  `interval` < 5;
```
 Otherwise simply export the entire *archive* table.

You would need to write your own simple script to process the data,
using the appropriate consolidation method for each variable as shown in the sql code below.

## Consolidate Data using the Database

There are enough differences between SQLite and MySQL, that the code needs to be slightly different.
Consequently, the scripts are listed separately.

These scripts are written to work with the default "weewx" database content.
If your database content is different then you will need to edit the list in the main consolidation phase.

### Description of Stages.

#### Prepare the input data table
Create a table with a copy of the input archive,
We work from a copy so that we can modify it without touching the main archive table.
#### Create the interim consolidated table
Populate table _c5_ with the easily generated data.

Most of the data consolidation is handled by built-in functions of the sql language,
but extra handling is required for wind data.
#### Process the average wind vector

Average wind is calculated as a vector average of wind speeds and directions.

#### Process the Wind Gust direction
Wind _gust_ consolidation is not done, since the gust _speed_ is
the  maximum during the consolidation period. This code
extracts the direction at the time at which the maximum was observed.
 	
There is an ambiguity with duplicate maximum gusts resulting in multiple records for the one 5-minute interval.
There is no sensible consolidation to pick a suitable direction for two or more equal gusts:
+ if their directions are close then an average is not much different from either value
+ if they are far apart then an average is simply wrong.

So, we arbitrarily choose the first one by telling the insert to ignore duplicated dateTimes

#### Merge the consolidation table back into the archive
This is the final stage and code for it is given separately at the end of this page.


### SQLite version
This code produces interim tables that can be easily inspected later to confirm calculations.
Using temporary tables does not seem to affect speed.

~~~ SQL
-- create a temporary copy fo the archive to manipulate
-- if want to check the operation then you might like to extract
-- a short time, like a month or two worth of data. (to do that,
-- uncomment the two lines with a date restriction

CREATE  TABLE asmall AS SELECT * FROM
    archive
WHERE
    `interval` < 5
--         AND dateTime > STRFTIME('%s', '2017-01-01 00:00:00')
--         AND dateTime <= STRFTIME('%s', '2018-01-01 00:00:00')
        ;

ALTER TABLE asmall ADD COLUMN dt5m INT;
UPDATE asmall SET dt5m = FLOOR((dateTime-30) / 300);

CREATE UNIQUE INDEX ix_a_dt on asmall (dateTime );
CREATE INDEX ix_a_dt5 ON asmall (dt5m );
CREATE INDEX ix_a_speed ON asmall (windGust );

-- create first temporary table for wind vector averaging

CREATE  TABLE wind (
    dateTime INT PRIMARY KEY,
    windDir DOUBLE,
    xvec DOUBLE,
    yvec DOUBLE
);

INSERT INTO wind 
  SELECT MAX(dateTime) AS dateTime,
    NULL AS windDir,					-- default if speed is zero
	AVG(windSpeed * COS(RADIANS(windDir))) AS yvec,
	AVG(windSpeed * SIN(RADIANS(windDir))) AS xvec
    FROM asmall
    GROUP BY dt5m  ;

-- create the temp table containing the newly consolidated results
-- the column names here should match the original database archive table,
-- for ease of re-inserting into archive

CREATE  TABLE c5 AS
  SELECT MAX(dateTime) AS dateTime,
    dt5m,
    usUnits,
    5 AS `interval`,
    AVG(barometer) AS barometer,
    AVG(pressure) AS pressure,
    AVG(altimeter) AS altimeter,
    AVG(inTemp) AS inTemp,
    AVG(outTemp) AS outTemp,
    AVG(inHumidity) AS inHumidity,
    AVG(outHumidity) AS outHumidity,
    AVG(windSpeed) AS windSpeed,
    NULL AS windDir,
    MAX(windGust) AS windGust,
    NULL AS windGustDir,
    AVG(rainRate) AS rainRate,
    SUM(rain) AS rain,
    AVG(dewpoint) AS dewpoint,
    AVG(windchill) AS windchill,
    AVG(heatindex) AS heatindex,
    SUM(ET) AS ET,
    AVG(radiation) AS radiation,
    AVG(UV) AS UV,
    AVG(extraTemp1) AS extraTemp1,
    AVG(extraTemp2) AS extraTemp2,
    AVG(extraTemp3) AS extraTemp3,
    AVG(soilTemp1) AS soilTemp1,
    AVG(soilTemp2) AS soilTemp2,
    AVG(soilTemp3) AS soilTemp3,
    AVG(soilTemp4) AS soilTemp4,
    AVG(leafTemp1) AS leafTemp1,
    AVG(leafTemp2) AS leafTemp2,
    AVG(extraHumid1) AS extraHumid1,
    AVG(extraHumid2) AS extraHumid2,
    AVG(soilMoist1) AS soilMoist1,
    AVG(soilMoist2) AS soilMoist2,
    AVG(soilMoist3) AS soilMoist3,
    AVG(soilMoist4) AS soilMoist4,
    AVG(leafWet1) AS leafWet1,
    AVG(leafWet2) AS leafWet2,
    AVG(rxCheckPercent) AS rxCheckPercent,
    AVG(txBatteryStatus) AS txBatteryStatus,
    AVG(consBatteryVoltage) AS consBatteryVoltage,
    SUM(hail) AS hail,
    AVG(hailRate) AS hailRate,
    AVG(heatingTemp) AS heatingTemp,
    AVG(heatingVoltage) AS heatingVoltage,
    AVG(supplyVoltage) AS supplyVoltage,
    AVG(referenceVoltage) AS referenceVoltage,
    AVG(windBatteryStatus) AS windBatteryStatus,
    AVG(rainBatteryStatus) AS rainBatteryStatus,
    AVG(outTempBatteryStatus) AS outTempBatteryStatus,
    AVG(inTempBatteryStatus) AS inTempBatteryStatus
    FROM asmall
    GROUP BY dt5m;

	
CREATE UNIQUE INDEX ix_c5_dt on c5 (dateTime );
CREATE INDEX ix_ac5dt5 ON c5 (dt5m );
CREATE INDEX ix_c5_speed ON c5 (windGust );

-- now do the vector averaging
UPDATE wind 
	SET	windDir = DEGREES(ATAN2(yvec, xvec) )
    WHERE xvec IS NOT NULL;
	
UPDATE wind 
	SET windDir = windDir + 360.0
	WHERE windDir < 0.0;

UPDATE c5 
	SET  windDir = (
		SELECT wind.windDir
			FROM wind 
			WHERE c5.`dateTime` = wind.`dateTime`
		);

-- wind gust averaging is not done, since the gust is
-- the  maximum during the consolidation period. This code
-- extracts the direction at the time at which the maximum was observed.

CREATE  TABLE gusts (
    dateTime INT PRIMARY KEY,
    dt5m INT,
    windGustDir DOUBLE,
    windGust DOUBLE
);  
    
-- these indexes are not really needed but seem to speed up 
-- operations on large datsets.
CREATE INDEX ix_gs_dt5 on gusts (dt5m );
CREATE INDEX ix_gs_gspeed  on gusts (windGust);

INSERT  OR  IGNORE INTO gusts
	SELECT 
		c5.dateTime AS dateTime,
		a.dt5m AS dt5m,
		a.windGustDir AS windGustDir,
		a.windGust AS windGust		-- just for checking
		FROM asmall AS a JOIN c5
		ON
			c5.windGust = a.windGust AND
			c5.dt5m = a.dt5m ;

UPDATE c5 
    SET windGustDir = (
    	SELECT g.windGustDir
			FROM gusts AS g
			WHERE c5.dateTime = g.dateTime
		);
~~~

### MySQL version

The MySQL code is given on the page:
[Consolidation using MySQL](Consolidation-using-MySQL)

### Merge data back into archive

Choose which version of the database you are going to update,
which could be a backup copy or the live version.

At this stage you should have the _c5_ table with consolidated data, ready to insert back into the database.

The following process will:
1. remove the old short interval data,
1. insert the newly consolidated data

#### Remove the old data
On either system, run the command...
~~~ SQL
DELETE FROM archive WHERE `interval` <=4 ;
~~~

#### Transfer consolidated data
Run the appropriate SQL commands.
SQLite and MySQL are quite different.
Because SQLite does not support the _DROP COLUMN_ 
option in ALTER TABLE, you need to have the table columns spelt out in full.
If your _archive_ table is different then you will need to adjust accordingly.

##### SQLite version
The order in which these columns are given must match the actual order in the archive.
The process does not match against the names.
~~~ SQL
INSERT INTO archive
	SELECT 
		`dateTime`, usUnits, `interval`,
		barometer, pressure, altimeter,
		inTemp, outTemp,
		inHumidity, outHumidity,
		windSpeed, windDir, windGust, windGustDir,
		rainRate, rain, dewpoint,
		windchill, heatindex, ET, radiation, UV,
		extraTemp1, extraTemp2, extraTemp3,
		soilTemp1, soilTemp2, soilTemp3, soilTemp4,
		leafTemp1, leafTemp2, extraHumid1, extraHumid2,
		soilMoist1, soilMoist2, soilMoist3, soilMoist4,
		leafWet1, leafWet2,
		rxCheckPercent, txBatteryStatus, consBatteryVoltage,
		hail, hailRate,
		heatingTemp, heatingVoltage, supplyVoltage, referenceVoltage,
		windBatteryStatus, rainBatteryStatus,
		outTempBatteryStatus, inTempBatteryStatus
	FROM c5
    ORDER BY dateTime;

~~~

##### MySQL version

~~~ SQL
ALTER TABLE c5 DROP COLUMN dt5m;
~~~
At this stage the columns of tables _c5_ and _archive_ should match.
Check, either by using the GUI or the following commands (run each command in a separate tab so flipping between them reveals any differences clearly;
~~~ SQL
SHOW COLUMNS FROM c5;
SHOW COLUMNS FROM archive;
~~~
Then update the archive...
~~~ SQL
INSERT INTO archive
	SELECT * FROM c5
    ORDER BY dateTime;
~~~

