Bootstrap template with simple gauges (for Weewx v3)
==================

This skin uses the Bootstrap front end and some home made generators to create simple gauge representations of the current weather, and a colourful monthly history summary.

To see a live example of this skin, visit this weather site for Royston, UK:

http://dajda.net

Before we begin...
--------
1. This has been tested on weewx version 3.0.1 running on Ubuntu linux 14.04 (PC) and
on Raspbian Wheezy (Raspberry Pi).</li>
2. Works with sqlite, mysql, and any other weewx supported database.</li>

Download
--------
* https://github.com/brewster76/fuzzy-archer/archive/v2.2-beta.tar.gz, or 
https://github.com/brewster76/fuzzy-archer/archive/v2.2-beta.zip

Latest development code can be found here: 
https://github.com/brewster76/fuzzy-archer/releases

Install
--------
Use the Weewx extension installer:

~~~~~~
wee_extension --install [wherever you've put the .tar.gz archive]
~~~~~~

Web content is stored in public_html/Bootstrap.

More information
--------

Can be found on the github page here: https://github.com/brewster76/fuzzy-archer/blob/master/INSTALL


Screenshots
-----

1. *Front page*
![Front page](https://sourceforge.net/p/weewx/wiki/Bootstrap/attachment/bootstrap01.png)

2. *History page*
![History page](https://sourceforge.net/p/weewx/wiki/Bootstrap/attachment/bootstrap02.png)