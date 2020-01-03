
This page is meant as a temporary repository for details to allow people to install and test the beta-releases of weewx 4. The focus will be on Python 3 and I will use the example of testing weewx version 4.0.06b
## Background
These notes are assuming you have one system, that currently has a working version of Weewx 3.x.

The aim is to install in parallel so that you can switch between both, and errors in weewx 4 will not cause issues with the existing installation.

Your system will probably have python 2 and 3 both installed, and the default (/usr/bin/python) will most likely use python2. 
Most OS systems provide a way to change this, but I would strongly suggest you leave it unchanged as it would likely break anything that does not work with python 3. These notes assume you need python 2 as the default, but want to test weewx4 using python3.


The Documentation applying to weewx version 3 on the main web site does not give complete instructions for V4, so follow those given here

## Downloading
* download the latest gzipped tar file from http://weewx.com/downloads/development_versions/
* expand it somewhere under your home folder
* explore to the `docs` folder, where the main instructions you need are in the `setup.htm` file - open it with your favourite html browser and it will describe how to do `a setup.py install`, which is the only method supported at the moment.
* Note that the `readme.htm` file refers to instructions for different OS - **do not try these**  as they may be for packages that have not been prepared yet (as of beta6).  This will presumably change as the beta development proceeds.

## Installing
* In the docs explaining setup.py, find the list of prerequisites appropriate to your OS (or the nearest you can find), and click on the Python 3 tab
* install all the recommended packages, according to your system.
### Configure weewx 4
* cd to the `weewx-4.0.06b` folder where you extracted your download.
* edit the setup.cfg file. The only decision is where to safely install it.  The default is `/home/weewx`, which is a good choice, **unless **your weewx3 installation is there already. Something like  `/home/weewx/beta4` should be safe.
* Now build and install, based on which python you want to work with:
~~~~~
python3 ./setup.py build
sudo python3 ./setup.py install
~~~~~
* I allow the installer to create a default `weewx.conf` file and then merge in my specific values.
* cd to `/home/weewx/beta4`, or wherever you chose, and edit the `weewx.conf` file there.
* I would recommend using a different database from the working weewx3 version - you could start afresh, or duplicate the current one. Better still, try initially with a new DB, and if it seems to be working then swap to a duplicate of your current one.
* think about other changes you might have made, such as DB schema or skins. Some of these will be apparent as you merge your existing `weewx.conf` values into the new file.
* For DB schema, you might want to configure that immediately, however skin changes can be gradually introduced as things are shown to be working.
## Running
* for initial testing, the program should be run standalone
~~~~~
sudo ./bin/weewxd  weewx.conf
~~~~~
This will (we hope) fill your display with startup and loop packets.
* this is good in the short term, but might be a bit overwhelming for long-term testing.
* You may want to install the OS-specific daemon startup code, but you should probably leave that until you are ready to switch over to v4 permanently.
* Provided your weather station stores archive records for you, it should be trivial to switch between v3 and v4, and allow each database to be populated with the values stored while the program was offline.
## Updating
* I haven't done this yet (so just guessing)
* download and extract the new version into a new directory
* copy over the `setup.conf` file and run the setup.py scripts as before.
* the install script makes a copy of your bin folder, naming it `bin.2020nnnnnnnnn` according to the current date/time.
* your customised `weewx.conf` should remain, but skin changes might need to be redone.