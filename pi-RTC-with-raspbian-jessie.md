Pi with raspbian Jessie and install of RTC

Up until Jessie I have had no problems with an RTC install with Jessie my old methods did not work.

Checking forums found that this was indeed a problem, but after cobbling together information I have the following advice that is working for me.

All of my attempts to install were wound back and any symbolic links generated removed until basically at a fresh Jessie install.

Three main bits of information from Marcus 15, Gordon77, and the README file at the following file location  Additional overlays and parameters are documented /boot/overlays/README, specifically at about line 290 for i2c-rtc information.

Jessie now supports out of the box RTC based on ds1307,ds3231,mcp7941x and three others, mine is based on the ds3231.

1.	After having booted to Jessie UI screen configure basic data as required including enabling i2c.
2.	```sudo nano /boot/config,txt```
3.	At the bottom of this file you should now see dtparam=i2c=on
4.	For my ds3231 unit add the line 
5.      ```dtoverlay=i2c-rtc,ds3231```
6.	Save and exit file
7.	```sudo nano /lib/udev/hwclock-set```
8.	comment out the lines
          ``` 
        if [ -e /run/system/system ]; then
        exit 0
	fi
        ```
9.	There is no need to add i2c-bcm2708,i2c-dev and rtc-ds1307 to the /etc/modules as before.
10.	/etc/modprobe.d/raspi-blacklist.conf has nothing in it
11.	I then got rid of the fake hwclock```sudo apt-get remove –purge fake-hwclock```
12.     Ensure NTP on```timedatectl set-ntp true```
13.     Connected to web check date and time and write to RTC ```sudo hwclock –w```
14.     Reboot and check date and time with ```timedatectl status```
15.     I then shutdown and removed lan and left off for 2 hrs and checked operation using monitor not ssh in.



