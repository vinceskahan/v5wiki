This is an attempt to outline the tests required to give adequate coverage for WeeWX 5 installations and upgrades.

## Installation

Clean install of WeeWX.  These are platforms with built-in python3.  Older platforms should work using a `pip` install, but only by installing python3 separately.

|                  | pip | deb | rpm |
|------------------|-----|-----|-----|
| MacOS            | x   |     |     |
| Debian 9         | x   | x   |     |
| Debian 10        | x   | x   |     |
| Debian 11        | x   | x   |     |
| Debian 12        | x   | x   |     |
| Ubuntu 16.04 LTS | x   | x   |     |
| Ubuntu 18.04 LTS | x   | x   |     |
| Ubuntu 20.04 LTS | x   | x   |     |
| Ubuntu 22.04 LTS | x   | x   |     |
| Rocky 8          | x   |     | x   |
| Rocky 9          | x   |     | x   |
| SUSE 15          | x   |     | x   |
| FreeBSD          | x   |     |     |
| OpenBSD          | x   |     |     |
| Windows 10       |     |     |     |

## Upgrades

| from                    | to             |
|-------------------------|----------------|
| weewx4,python2          | weewx5         |
| weewx4,python3          | weewx5         |
| weewx5                  | weewx5         |
| weewx4,python3,setup.py | weewx5,pip     |
| weewx4,python3,deb      | weewx5,deb     |
| weewx4,python3,rocky8   | weewx5,rocky8  |

## Supported but *not* automated

* weewx4,python2,debian10 to weewx5,debian10
* weewx4,python2,centos7 to weewx5,rocky8
* weewx5,python3,rocky8 to weewx5,rocky9
