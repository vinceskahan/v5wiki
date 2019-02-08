# WeeWX on Windows

These are instructions for running WeeWX on Windows.  Running WeeWX on windows is very experimental.  It works reliably for the Simulator and the Dyacon CM1 driver, which uses modbus.  It has not been tested extensively.

## Download and install Python

Download and install python 2.7 for all users
```
https://www.python.org/downloads/
```

In a windows cmd shell, install the python dependencies:
```
python -m pip install configobj Image Cheetah uptime pyephem pyusb
```

## Prepare a directory for WeeWX

In a windows cmd shell with admin rights:
```
mkdir c:\users\weewx
cacls c:\users\weewx /t /e /g Everyone:f
```

Everything else can be done in a normal cmd shell

## Download WeeWX

You must use the `windows` branch, either from a zip file of the `windows` branch, or by cloning the repository then checking out the `windows` branch.

Either download a zip file of the `windows` branch:
```
https://github.com/weewx/weewx/archive/windows.zip
unzip windows.zip
cd windows
```

OR clone the repository then checkout the `windows` branch:
```
git clone https://github.com/weewx/weewx.git
cd weewx
git checkout windows
```

## Install WeeWX

Use the destination `c:\Users\weewx` in `setup.cfg`, then use python to install:
```
copy util/windows/setup.cfg .
python setup.py install
```

## Run WeeWX

To run WeeWX, change to the installation directory then invoke `weewxd`:
```
cd c:\users\weewx
python bin\weewxd weewx.conf
```
