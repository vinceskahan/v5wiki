##Minimize writes for systems using SD cards or SSDs##

weewx writes to disk for the following:

* Images and HTML files are generated every archive interval
* Messages in system log files
* Meteorological data are saved to sqlite database

To eliminate writes, do the following:

* Save reports to a temporary file system (tmpfs or ramdisk)
* Save system logs to a temporary file system or remote syslog server
* Save meteorological data to a remote mysql server
