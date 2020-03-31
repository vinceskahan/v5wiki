# Compatibility matrix for weewx

This page is an attempt to make sense of the dependencies and upgrade paths for weewx.  Although weewx (still) has few dependencies itself, the python2/python3 change introduced significant complexity, and the libraries weewx uses (notably python imaging and cheetah) vary widely between platforms.

## Dependencies

This is an enumeration of the weewx dependencies for each major operating system release.

| os | python2 | python3 |
|---|---|---|
| debian10 | apt-get install python-configobj<br>apt-get install (python-imaging:python-pil:python-pillow)<br>apt-get install python-cheetah<br>apt-get install python-usb<br>apt-get install python-serial | apt-get install python3-configobj<br>apt-get install python3-pil<br>apt-get install python3-cheetah<br>apt-get install python3-usb<br>apt-get install python3-serial |
| debian9 | apt-get install python-configobj<br>apt-get install python-cheetah<br>apt-get install python-usb<br>apt-get install python-serial | apt-get install python3-configobj<br>apt-get install<br>apt-get install python3-usb<br>apt-get install python3-serial |
| debian8 | apt-get install python-configobj<br>apt-get install python-cheetah<br>apt-get install python-usb<br>apt-get install python-serial |  |
| el8 | | |
| el7 | | |
| opensuse | | |
| sel | | |

## Upgrade paths

This is an enumeration of the upgrade paths for weewx, for various operating system packages.

