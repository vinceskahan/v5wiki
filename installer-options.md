# Installer options

WeeWX started with a non-standard use of python's `setup.py`.  The intent was to put all of the WeeWX bits in a single directory - installed code, configurations, skins, and data.  That is not how installation of a 'standard' python module works, so `setup.py` and `setup.cfg` were adjusted to make it behave that way.

Support was later added for per-platform packages, such as `deb` for Debian, Ubuntu, and Mint as well as `rpm` for Redhat/CentOS and SuSE.

These notes about the pros/cons of different mechanisms for installing WeeWX.  

## weewx-ified setup.py (single directory install)
  + easy, but with many caveats: `sudo python3 setup.py install`
  + everything in single directory
  + easy to maintain multiple concurrent versions for development
  - manual configuration for init.d/systemd
  - manual configuration for ancillary configs
  - full path or setting of PATH required to run

## pip
  + easy: `pip install weewx`
  + works great for pure python apps
  + works with python2 or python3
  + works on any platform on which python runs
  + mechanisms for system-wide or per-user install
  + the use of wee* makes weewx immune to name clashes?
  - no mechanism init.d/systemd/rc
  - no mechanism for udev scripts
  - entry points are buried with other python commands
  - where do the config files and skins live?
  - where do the data live?
  - no standard for ancillary configs such as logrotate, rsyslog, udev, nginx
  - no mechanism for installing multiple concurrent versions

## platform packages
  + easy: `sudo yum install weewx` or `sudo apt install weewx`
  + for users, it looks like any other application
  + configs live in /etc/weewx
  + data live in /var/lib/weewx
  + ancillary configs live in /etc/weewx and just need symlink to activate
  + entry points in /usr/bin so no PATH futzing required
  - requires root to install
  - requires tweaks to run as non-root user (this could be fixed)
