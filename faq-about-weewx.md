
### WeeWX in a nutshell

See the top-level [Weewx Home Page](http://www.weewx.com) description

### How is the software written

Other than a few system startup configuration files, WeeWX is written in pure python, with python3 preferred and python2 still supported as of this writing.  Given that python2 is past end-of-life, it can be expected that python2 compatibility will be dropped in some future version of WeeWX.

### How is WeeWX architected

The best description is in the Introduction section of the [Customization Guide](http://www.weewx.com/docs/customizing.htm).

### Some terminology you will see

* drivers - collect data from various primary sources
* extensions - add capabilities to a WeeWX installation
* services - collect and emit data
* skins - contain the templates for generating report
* uploaders - transfer data to online services

You may have 'one' driver per weewx installation, but multiple extensions/services/skins/uploaders per installation.

### Prerequisite software

WeeWX relies on the python PIL image libraries and the Cheetah templating engine, and either sqlite3 or mysql/mariadb for its database.   You may optionally add pyephem to get more accurate astronomical data (sunrise/sunset, phase of the moon etc.) in your reports
