# Strategies for publishing data from weeWX

This is an outline of strategies for getting data from hardware into reports or reporting services using weeWX.  Let's start with the use cases.

## Use case 1: Default weeWX installation

The most basic weeWX installation emits a set of web pages (the StandardReport) every archive interval.  The web pages are simple HTML with plots rendered as PNG images.  There is minimal use of JavaScript, and no dependencies on any web-development frameworks.  Data are updated at most every archive interval.  There is no automatic client refresh, or notification to client when new data arrive.

Goals:

* Basic functionality with any type of hardware
* Simple implementation that is easy to understand and extend
* Illustrate and exercise weeWX concepts and capabilities
* No network dependencies (e.g., no content delivery network for fonts or images)
* No framework dependencies (e.g., no jQuery or additional fonts)
* No additional software required (e.g., web server)

## Use case 2: Custom reports

Use weeWX to format data as required by existing templates, possibly generating plots and additional files.  In this case, the templates have been created and maintained independently of weeWX.  These templates typically require one or more data files that they then read.  For example, many of these templates expect sensor readings in a `realtime.txt` or `customraw.txt` file.  They may also use plotted data rendered as image files.

Since data file is generated/updated using the StdReport mechanism, the templates are updated at most every archive interval.

Examples:

* Saratoga
* Leuven
* Steel-Series
* Weather-for-you

## Use case 3: Feed into a data aggregation/reporting service

In this scenario, weeWX feeds into a separate service that aggregates, stores, and reports data. 

Examples:

* weeRT
* Influx
* EmonCMS
* SEG
* ThingSpeak
* meteotemplate

## Use case 4: Local display of data in real time

emit data to json file on every loop.  javascript plotting polls the json file for changes, reloads data on each change.

## Use case 5: Remote display of data in near real time

This can be done with a combination of elements from the other use cases.

For example, the Saratoga template can be updated on every LOOP packet by emitting a realtime.txt file then transferring that file at each LOOP packet (instead of once per archive interval).

As another example, a dashboard of grafana plots will update as soon as new data arrive in influx.  Use the influx uploader and set the binding to loop instead of archive.

## Things to consider

### Plotting the data

static plot images vs dynamic javascript plotting

for javascript plotting: read data from json/csv file vs read data from a server

current values vs historical values

ability to aggregate historical values

### Transferring and synchronizing data

mechanism: ftp/ftps/rsync vs restful

frequency: loop vs archive
