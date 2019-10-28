This is the main script for archive consolidation using MySQL.

Using temporary tables makes it rather faster, so are used by default. They are automatically deleted at the end of the session, so you might want to remove the TEMPORARY keyword from the table creation statements (there are 4) and manually drop them later.

``` SQL
-- create a temporary copy of the archive to manipulate
-- if want to check the operation then you might like to extract
-- a short time, like a month or two worth of data. (to do that,
-- uncomment the two lines with a date restriction)

CREATE TEMPORARY TABLE asmall LIKE archive;
INSERT INTO asmall
	SELECT * FROM archive
		 WHERE  `interval` < 5
-- 		 	and dateTime  > unix_timestamp("2017-11-01 00:00:00")
-- 		 	and dateTime <= unix_timestamp("2018-01-01 00:00:00")
		;

ALTER TABLE asmall ADD COLUMN dt5m INT AFTER dateTime;
UPDATE asmall SET dt5m = FLOOR((dateTime-30) / 300);
CREATE INDEX ix_a_dt5 ON asmall (dt5m );
CREATE INDEX ix_a_speed ON asmall (windGust );

-- create first temporary table for wind vector averaging

CREATE TEMPORARY TABLE wind (
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

CREATE TEMPORARY TABLE c5 LIKE asmall;
INSERT INTO c5
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

CREATE TEMPORARY TABLE gusts (
    dateTime INT PRIMARY KEY,
    dt5m INT,
    windGustDir DOUBLE,
    windGust DOUBLE
);  

-- these indexes are not really needed - the time saved is similar to
-- the extra time taken in their creation.
CREATE INDEX ix_gs_dt5 on gusts (dt5m );
CREATE INDEX ix_gs_gspeed  on gusts (windGust);


INSERT IGNORE INTO gusts
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
```

[Back to previous page](Data-Consolidation---changing-sampling-interval)