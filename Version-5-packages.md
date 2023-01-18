These are design notes for platform packages (deb, rpm) for the V5 release of weewx.

## locations

| name | nominal value |
|---|---|
|WEEWX_ROOT   | not used |
|BIN_ROOT     | /usr/share/weewx |
|CONFIG_ROOT  | /etc/weewx |
|SKIN_ROOT    | /etc/weewx/skins |
|SQLITE_ROOT  | /var/lib/weewx |
|HTML_ROOT    | /var/www/weewx |
|DOC_ROOT     | /usr/share/doc/weewx |
|EXAMPLE_ROOT | /usr/share/doc/weewx/examples |
|USER_ROOT    | /usr/share/weewx/user |
|PID          | /var/run/weewx.pid |
|LOG          | /var/log/weewx/weewx.log |

## entry points

There will be scripts for weectl, weewxd, wee_reports, etc. in `/usr/bin` that use values from `/etc/default/weewx` to invoke the corresponding python entry points.

## user

Installation will create a system user `weewx` with group `weewx`.

To facilitate permissions on physically attached devices, an appropriate udev rules file will be installed.

TODO: verify that the udevadm or other mechanism properly enables the udev rules

## daemonization

If systemd exists, then a systemd unit file will be installed, otherwise a sysv script will be installed.

## weewx-multi

The daemon config (systemd or sysv) will respect the weewx-multi conventions so that multiple weewx instances can be run with a minimum of system modification.

## miscellaneous

The post installer will run python on all python code in order to pre-compile every weewx module.

