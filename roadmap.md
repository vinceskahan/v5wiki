# Roadmap for weeWX

## Features to include in the next release

## Wish list of features

* Eliminate the need for Ftp/skin.conf and Rsync/skin.conf.  More generally,
do not require a skin.conf file for each report.  In the case of Ftp and Rsync
one can simply put the generator_list in weewx.conf as part of the report
definition.  A report with no skin.conf should run with sane defaults.
Templates should also be "drop in." If they appear in the directory, they
get used.

* Minor cleanup to the Standard skin:
 - improve layout, possibly like http://www.kanonbra.com/veret/
 - punt the buttons for week/month/year, use regular html links instead
 - include a table instead of duplicating html code for stats
 - make the info section easier to grok with a structured layout, perhaps
    similar to weather display or cumulus default templates

* Offer tags to support JSON. In particular, vectors. A tag like
$day.outTemp.vector would offer a vector of temperatures for the day.
Aggregation would be nice.

* A QC "Spike" detector.

* Harden up the MySQL database connection.

* Offer "cumulative" as an aggregation type.

* Allow rain-to-date to be added to a partial rainyear.

* More dynamic access to the database. Check out
Werkzeug. http://werkzeug.pocoo.org [1/27/2017. This is essentially weeRT]

* Dynamic web updates, using AJAX, or Meteor.

* Port to Windows.

* Add log_success/log_failure parameters for subsystems other than just restx.

## Graphing system wish list:

* Allow variable y-axis labelling. Right now it's hardwired to every other major tick mark.

* Fix scaling for aggregate interval of a week, so it starts on a Sunday.

* user suggestions are here:
    https://groups.google.com/forum/#!topic/weewx-user/0hGKjHwD3J0

* image background for plots - useful when overlaying a wind rose on a satellite image of the earth, for example.

* wind direction plot over time (e.g., as in weather display)
    http://jaxweather.net/WeatherImages/WD/dirplot.gif

* multiple y scales

* "current wind dial"
    http://www.hasslosa.se/weather/wd/wx3.php

* other wind rose variants
    http://en.wikipedia.org/wiki/Wind_rose
    http://www.therodericks.id.au/saratoga/wxtrends.php

* add ability to specify start date as well as time length on plots

* compound graphs such as forecast graphs from yr.no

* wind summary graphs such as those from windfinder
   http://www.windfinder.com/windstats/windstatistic_raa.htm
