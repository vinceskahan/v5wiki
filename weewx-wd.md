# WEEWX-WD

WeeWX-WD is a WeeWX skin that will generate the files required drive Weather Display Live (WDL) and the Carter Lake/Saratoga HTML templates.

## Features

WeeWX-WD produces the following files required by WDL and Carter Lake/Saratoga HTML templates:
* WDL
  * clientraw.txt
  * clientrawextra.txt
  * clientrawdaily.txt
  * clientrawhour.txt
* Carter Lake/Saratoga templates
  * testtags.php

## Limitations

Whilst WeeWX-WD produces the necessary data files required by WDL and the Carter Lake/Saratoga HTML templates these files are only updated at the end of every WeeWX archive period. Therefore live updates from LOOP data are not possible at this time. The live update aspects of the WDL and Carter Lake/Saratoga HTML templates will still work, but the data will not change except at the end of every archive period.

WeeWX has been extended through WeeWX-WD to provide most of the tags provided by WeatherDisplay (WD); however, at this time there are a number of tags that are not yet provided and there are likely a number of tags that never will be provided. A list of these outstanding tags are included on the WeeWX-WD Wiki Tags page.

## Prerequisites

WeeWX-WD requires a fully functioning installation of WeeWX version 3.2.0 or greater and is fully compatible with WeeWX 4.0.0 or later under both Python2 and Python3. The operation of WeeWX-WD is unaffected by the database server used by WeeWX, nor is it effected by the version of PHP installed or the presence or otherwise of a web server. However, in order to use the WeeWX-WD generated files to drive WDL or the Carter Lake/Saratoga templates then these additional applications/templates must be installed and configured. The Carter Lake/Saratoga templates requires the WD plugin, a supporting icon set and a PHP GD capable web server if weather graphics such as the dashboard thermometer is to be displayed. Detailed instructions are available from the Saratoga Weather

## Project Website

The current project website and latest version of WeeWX-WD can be found at the [WeeWX-WD GitHub repo](https://github.com/gjr80/weewx-weewx-wd). The [original project website](https://bitbucket.org/ozgreg/weewx-wd) can be found on BitBucket.
