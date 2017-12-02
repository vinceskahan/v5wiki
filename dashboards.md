# Strategies for publishing data from weeWX

**WARNING: Some of the components described in this page are experimental or not yet implemented!!**

This is an outline of strategies for getting data from hardware into reports or reporting services using weeWX.

Let's start with some scenarios:

## Use case 1: Default weeWX installation

A default weeWX installation emits a set of web pages (the StandardReport) every archive interval.  The web pages are simple HTML with plots rendered as PNG images.  There is minimal use of JavaScript, and no dependencies on any web-development frameworks.  Data are updated at most every archive interval.  There is no automatic refresh for clients viewing the report, nor notification to client when new data arrive.

*Update frequency:* Report is generated/updated once per archive interval.  

*Local/Remote:* Report can be copied/synchronized to a remote location using FTP, FTPS, or rsync.

Goals:

* Basic functionality with any type of hardware
* Simple implementation that is easy to understand and extend
* Illustrate and exercise weeWX concepts and capabilities
* No network dependencies (e.g., no content delivery network for fonts or images)
* No framework dependencies (e.g., no jQuery or additional fonts)
* No additional software required (e.g., web server)

## Use case 2: Custom weeWX reports

Generate reports using the built-in plotting and template mechanisms built-in.  These reports consist of text, RSS XML, HTML, and image files.  Templates are written using the Cheetah and dot-code syntax built-in to weeWX.

*Update frequency:* Reports are generated/updated once per archive interval.  

*Local/Remote:* Reports can be copied/synchronized to a remote location using FTP, FTPS, or rsync.

*Examples:* Responsive, Bootstrap, Byteweather, Flat, Niculskin, exfoliation, simple, amphibian

## Use case 3: Reports from non-weeWX template frameworks

Use weeWX to generate a data file in the format required by a non-weeWX template, possibly generating plots and additional files in the process.  In this case, the templates have been created and maintained independently of weeWX.  These templates typically read data from one or more text files.  For example, many of these templates expect sensor readings in a `realtime.txt` or `customraw.txt` file.  They may also use plotted data rendered as image files.

*Update frequency:* Reports are updated every archive interval.

*Local/Remote:* Reports can be copied/synchronized to a remote location using FTP, FTPS, or rsync.

*Examples:* Saratoga, Leuven, Steel-Series, Weather-for-you

## Use case 4: Local display of data with near real time updates

In this scenario, weeWX emits data at each LOOP, typically as fast as the hardware produces it.  The reporting system receives the data and displays it immediately, typically pushing it to any clients that are viewing the report.

For example, weeWX might save LOOP data to a json file, then JavaScript plots poll the json file for changes, reloading the file and updating the display on each change.

*Update frequency:* Data are updated as frequently as the hardware sends it (every LOOP packet).

*Local/Remote:* Typically local, especially if the updates happen frequently.

*Examples:* mesowx, InfluxDB+Grafana

## Use case 5: Feed into a data aggregation/reporting service

In this scenario, weeWX feeds into a separate service that aggregates, stores, and reports data. 

*Update frequency:* Data are updated as frequently as the hardware sends it (every LOOP packet), or at each archive interval.

*Local/Remote:* The report display can be on the same computer running weeWX, same network as weeWX, or in the cloud.

*Examples:* weeRT (EXPERIMENTAL), InfluxDB, EmonCMS, SEG, ThingSpeak, meteotemplate

## Things to consider

### Plotting the data

static plot images vs dynamic javascript plotting

for javascript plotting: read data from json/csv file vs read data from a server

current values vs historical values

ability to aggregate historical values

### Transferring and synchronizing data

mechanism: ftp/ftps/rsync vs restful

frequency: loop vs archive
