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

## how the platforms use weectl

debian
```
V4:
wee_config --version
V5:
weectl --version

install_weewxconf
V4:
wee_config --install --dist-config=$cfgfile --output=$cfgfile \
    --driver=$driver --unit-system=$units --no-prompt --no-backup
V5:
weectl station reconfigure --config=$cfgfile --driver=$driver --units=$units \
  --location="$location" --latitude=$lat --longitude=$lon \
  --altitude=$altitude,$altitude_unit --register=$register $station_url_param \
  --no-prompt --no-backup

merge_weewxconf
V4:
wee_config --upgrade --config=$cfgfile-$OLDVER --dist-config=$cfgfile-$NEWVER --output=$cfgfile --no-prompt --no-backup
V5:
weectl station upgrade --config=$cfgfile --dist-config=$cfgfile-$NEWVER --what=config --no-prompt --no-backup
```

redhat+suse
```
new_install
V4:
wee_config --install --dist-config=/etc/weewx/weewx.conf.dist --output=/etc/weewx/weewx.conf --driver=weewx.drivers.simulator --no-prompt --no-backup
V5:


upgrade
V4:
wee_config --upgrade --config=%{cfg_file}-$OLDVER --dist-config=%{cfg_file}.dist --output=%{cfg_file} --no-prompt --no-backup
V5:

```
