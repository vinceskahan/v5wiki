## Raspberry Pi nuances and nits

New Raspberry Pi users typically have their own set of pi-specific Frequently Asked Questions.  This page captures some of the ones we see most on weewx-user


### Keeping the date+time accurate

WeeWX relies on an accurate date+time in order to function reliably.   Since the Raspberry Pi does not have a battery-backed motherboard clock, it only has a software kludge in the os that attempts to power up the system close to reasonable date+time.  This works for short powerdowns but not so if the pi powered down for a long time.   WeeWX tries to handle this condition by delaying startup until the operating system has a reasonable date+time, but it can only go so far in doing so.

You should either install an inexpensive realtime clock (RTC) module or configure time services on your pi to synchronize to the various Internet Time Servers that are available.  Taking the software path works fine if your pi is powered down only rarely.

More details are [here](Time-services) in the wiki.

### Installing a webserver

Many pi users are running Raspberry Pi OS Lite which does not have a web server in the default image.  If you run the Lite version of the os, you will need to install a webserver of your choice to the os.  Users typically install nginx or apache, although other webservers are available on the pi.

More details are [here](webserver) in the wiki.

### Minimizing SD writes

Historically there have been issues on older pi in which the SD cards had a rather high failure rate.   While this occurred most often on the old model-B pi, it does occur occasionally on the news zero/zerow/pi3/pi3+/pi4 cards that use micro SD cards that have weak power supplies.

Many people run their pi for many 'years' without SD failure, which requires having a good quality SD card and a good quality power supply.   Just spend the extra one dollar US for each to best ensure your SD card will work for many years without issue.

It is possible to tweak your os to 'need' to write to SD less.

More details are [here](Minimize-writes-on-SD-cards) in the wiki.