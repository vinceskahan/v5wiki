_**IN PROGRESS**_

This page is meant as a temporary repository for details to allow people to install and test the beta-releases of weewx 4. The focus will be on Python 3 and I will use the example of testing weewx version 4.0.06b
## Background
These notes are assuming you have one system, that currently has a working version of Weewx 3.x.

The aim is to install in parallel so that you can switch between both, and errors in weewx 4 will not cause issues with the existing installation.

Your system will probably have python 2 and 3 both installed, and the default (/usr/bin/python) will most likely use python2. 
Most OS systems provide a way to change this, but I would strongly suggest you leave it unchanged as it would likely break anything that does not work with python 3. These notes assume you need python 2 as the default, but want to test weewx4 using python3.


The Documentation applying to version 3 on the main web site does not give complete instructions, so follow those given here

## Downloading
* download the latest gzipped tar file from http://weewx.com/downloads/development_versions/
* expand it somewhere under your home folder
* explore to the _docs_ folder, where the main instructions are in the _setup.htm_ file - open it with your favourite html browser and it will describe how to do _a setup.py install_, which is the only method supported at the moment.
* Note that the _readme.htm_ file refers to instructions for different OS - **do not try these**  as they may be for packages that have not been prepared yet (as of beta6).  This will change as the beta development proceeds.

## Installing
* In the docs explaining setup.py, find the list of prerequisites appropriate to your OS (or the nearest you can find), and click on the Python 3 tab
* install all the recommended packages, according to your system.
### Configure weewx 4
* cd to the _weewx-4.0.06b_ folder where you extracted your download.
* edit the setup.cfg file. The only decision is where to safely install it.  The default is