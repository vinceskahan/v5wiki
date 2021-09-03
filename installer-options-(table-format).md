# Installer options

*Note: this is derived from Matthew's page [(link)](https://github.com/weewx/weewx/wiki/installer-options) created 2021-0903*

----
WeeWX started with a non-standard use of python's `setup.py`.  The intent was to put all of the WeeWX bits in a single directory - installed code, configurations, skins, and data.  That is not how installation of a 'standard' python module works, so `setup.py` and `setup.cfg` were adjusted to make it behave that way.

Support was later added for per-platform packages, such as `deb` for Debian, Ubuntu, and Mint as well as `rpm` for Redhat/CentOS and SuSE, following the os-expected filesystem locations.

These notes capture the pros/cons of different mechanisms for installing WeeWX.  


## Ease of installation and use
| setup | pip | packages | description |  notes |
| :---: | :---: | :---: | :---------------------------------- | :---------- |
| + | + | + | ease of weewx app installation | (1) (2) (3) (4) |
| - | o | + | automatic installation of corequisite python modules | (10)
| - | - | + | automatic installation of corequisite os packages | (9)
| - | ? | + | follows os file system standard and $PATH| |
|  manual | ? | + |startup subsystem hooks| (5) |
| o | o | o | log subsystem configuration | (6)
| o | o | o | logrotate configuration | (6) |
| o | o | o | udev configuration | not usually needed |
| - | - | - | webserver configuration | (13) |
| o | ?| + | example ancillary configs provided | (12) (14)|

## Architectural

| setup | pip | packages | description |  notes |
| :---: | :---: | :---: | :---------------------------------- | :---------- |
| - | - | - | runs as root | (8) |
| + | ? | - | supports multiple concurrent versions for development | |
| + | ? | - | everything in single directory | |
| o | o | + | support for python2 and python3 | (7)
| ? | + | - | mechanisms for system-wide or per-user install | (11) |
| n/a | + | + | the use of wee* makes weewx immune to name clashes? | |
| ? | - | ? | entry points are buried with other python commands | |


Notes
* (1) `python3 setup.py install`
* (2) `apt-get install weewx`
* (3) `yum install weewx`
* (4) `pip install weewx`
* (5) packages could autoconfigure this
* (6) manual configuration needed if logs are configured to go to a weewx-specific logfile
* (7) packages autoselect preferred/default version
* (8) some tweaks needed to support running as non-root user
* (9) if os+ver package names are well known
* (10) if os+ver python module names are known (cheetah/Cheetah, pil/pillow etc.)
* (11) some tweaks or use of sudo would be required to run as non-root
* (12) for packages - live in /etc/weewx and just need symlink to activate
* (13) defaults assume ./weewx tree under web docroot
* (14) for setup - examples are in source tree
     
----