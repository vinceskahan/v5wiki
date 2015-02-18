This Weewx skin (Weewx-WD) will generate for every Weewx archive Loop, the following files in a weather display compatible format

**WD**
WD\Clientrawextra.txt
WD\Clientrawdaily.txt
WD\Clientrawhour.txt

**Steel Series Gauges**
WD\Customclientraw.txt

**Carter Lake/Saratoga templates**
WD\testtags.php
WD\Clientraw.txt

These files are in turn, utilised in the generation of the Weather Display Live (WDL), Steel Series gauges and the Carter Lake/Saratoga Dashboards.
 
**Limitations**
Whilst Weewx-WD produces the necessary data files required by WDL, Steel Gauge and Carter Lake/Saratoga HTML templates these files are only updated at the end of every Weewx archive period. Therefore live updates from LOOP data are not possible at this time. The live update aspects of the WDL, Steel Gauge and Carter Lake/Saratoga HTML templates should still work, but the data will not change except at the end of every archive period.

Weewx has been extended through Weewx-WD to provide most of the tags provided by WD; however, at this time there are a number of tags that are not yet provided and there are likely a number of tags that never will be provided. A list of these outstanding tags are included on the Weewx-WD Wiki Tags page.

**Prerequisites**
Weewx-WD requires a fully functioning installation of Weewx version 2.5.0 or greater. The operation of Weewx-WD is unaffected by the database server used by Weewx, nor is it effected by the version of PHP installed or the presence or otherwise of a web server. However, in order to use the Weewx-WD generated files to drive WDL, the Steel Series gauges or the Carter Lake/Saratoga templates then these additional applications/templates must be installed and configured. The Carter Lake/Saratoga templates requires the WD plugin, a supporting icon set and a PHP GD capable web server if weather graphics such as the dashboard thermometer is to be displayed. Detailed instructions are available from the Saratoga Weather site.

**Extra observations**
Although Weewx has a substantial temperature measurement capability, Weather Display (WD) provides two calculated temperature related observations, Humidex and Apparent Temperature, that Weewx does not provide. To allow aggregate statistics and graphing of these additional observations they have been captured in extraTemp1 and extraTemp2 respectively in the Weewx archive and stats databases.

Whilst Weewx records both windSpeed and windGust data separately in the archive database, the stats database records a vector type wind which is a composite of windSpeed and windGust. Consequently, Weewx cannot provide aggregate statistics on windSpeed data alone. To provide aggregate statistics on windSpeed alone, an additional field, windAv, has been added to the Weewx statistics database. WindAv records data on the windSpeed observation only

**Project Website**
For additional information please visit the project website at  https://bitbucket.org/ozgreg/weewx-wd