# Troubleshooting Davis stations
This article covers the Davis VantageVue, VantagePro2, and Envoy.

## Establishing connectivity
If you are unable to get anything out of WeeWX, first check that you have connectivity to your
weather station. For the Davis stations, you can use a terminal emulator to run a simple test. Set it up to communicate using the appropriate port and baudrate. I like `minicom` because it can be run from through a simple TTY connection. The utility `screen` also works well. For example:

    minicom -b 19200 -D /dev/ttyUSB0

or, using `screen`:

    screen /dev/ttyUSB0 19200

First, press \<enter\> a few times, in order to wake up the console. Then type `TEST`, all in
capital letters. The characters will not be echoed back. Then press \<enter\> once more. The Davis
should echo back `TEST`.

If this works, then you have established connectivity with the Davis and the problem must lie elsewhere.

## Davis cp2101 converter problems
The USB converter used in the Davis VantagePro is known to have some "noise" problems. The symptom
is that the Linux kernel will disconnect from your old USB port claiming "EMI noise", and reconnect
to a new and different port, where WeeWX cannot find it. Here is a typical log output:

```
Nov 29 10:40:21 hummingbird kernel: [6661624.786792] hub 2-0:1.0: port 3 disabled by hub (EMI?) re-enabling...
Nov 29 10:40:21 hummingbird kernel: [6661624.786871] usb 2-3: USB disconnect, address 2
Nov 29 10:40:21 hummingbird kernel: [6661624.795778] cp2101 2-3:1.0: device disconnected
Nov 29 10:40:21 hummingbird weewx[25808]: VantagePro: Max retries exceeded while getting LOOP packets
... (messages elided)
Nov 29 10:40:22 hummingbird kernel: [6661625.352340] cp2101 2-3:1.0: cp2101 converter detected
Nov 29 10:40:22 hummingbird kernel: [6661625.528107] usb 2-3: reset full speed USB device using ohci_hcd and address 3
Nov 29 10:40:22 hummingbird kernel: [6661625.735497] usb 2-3: cp2101 converter now attached to ttyUSB1
```
In this example, the VantagePro was connected to `/dev/ttyUSB0`, but then reconnected to
`/dev/ttyUSB1`.

If you put ferrite coils on the USB connection, you will eliminate 90% of this problem. I did this
about 8 years ago, and have not had a problem since.

However, there is one final step that you can take to really harden up your system: install a
`udev` script that will create a symbolic link to the VantagePro USB port, whatever it might be.
With this approach, if the port jumps from `ttyUSB0` to `ttyUSB1`, the symbolic link will follow
it. You just specify port `/dev/vpro` in the configuration file `weewx.conf` and be done with it.

## Installing a udev script
Use a `udev` rule to ensure that a USB device always appears at the same location such as `/dev/vpro`,
instead of a specific location such as `/dev/ttyUSB0`.

For example, for my VantagePro2 weather station, I have installed a file
`/etc/udev/rules.d/vpro.rules` on my fit-PC that looks like this:

```
# Automount the VantagePro2 to port /dev/vpro.
# Install in /etc/udev/rules.d/vpro.rules
#
ACTION=="add", ATTRS{interface}=="CP2102 USB to UART Bridge Controller", SYMLINK+="vpro"
```

This rule says that when the USB port is plugged in (action `add`), and it has an attribute with
name interface that is equal to `CP2102 to UART Bridge Controller`, then add a symbolic link for its
physical port to `/dev/vpro`.

Here is a rule that works for my Serial-to-USB cable, made by "Y.C. Cable USA". It not only adds a
symbolic link `vpro`, but also sets the `chmod` permissions to `666`, allowing any user to read or
write to it.

```
# Automount Serial-to-USB cable to port /dev/vpro
# Install in /etc/udev/rules.d/cable.rules
#
ACTION=="add",ATTRS{idVendor}=="05ad",ATTRS{idProduct}=="0fba",MODE="0666",SYMLINK+="vpro"
```

Your devices may, and probably will, have different identifiers!! I can recommend this article,
"[Writing udev rules](http://www.reactivated.net/writing_udev_rules.html)," for how to find and
write an appropriate `udev` rule for your controller. (Note, however, that this article uses the old
`udevinfo `command, rather than the newer `udevadm` command.) In particular, run the command

```shell
udevadm info --attribute-walk --path $(udevadm info --query=path --name=/dev/ttyUSB0)
```

where `/dev/ttyUSB0` is the port (substitute your real USB port) the weather station is attached
to. It will print out various identifiers that can be useful in identifying your weather station to
`udev`. While the first example script above used a rule that matched attribute interface, others
are possible. For example, the second example, for the serial-to-USB cable, chose to match the
attribute `product`.

Once you have installed your `udev` rule, you can then set `port=/dev/vpro` in `weewx.conf`,
confident that it will always point to your weather station, no matter which USB port it is
actually attached to!

## WeeWX generates HTML pages, but it does not update them
If you are getting a symptom that everything appears normal, that is image and HTML files are
generated (look in the log to be sure) and sent to your webserver (if you have configured FTP or
rsync), but the values in the web pages are not being updated, it could be due to clock skew or
corrupt station memory.

### Clock skew
If the database contains a record with time stamp (the `dateTime` field) in the future, then
records from the station that are older than that future date will be ignored. How can the database
contain records from the future? Sometimes the computer clock is not set correctly. For example,
the Raspberry Pi has no clock, so if WeeWX saves data before the pi has synchronized its clock with
internet time servers, the records will have incorrect time stamps, some of which might be in the
future.

The solution is to delete any records with time stamp in the future. For a sqlite database, the
procedure looks like this:

```shell
cp /home/weewx/archive/weewx.sdb /home/weewx/archive/weewx.sdb.backup
sqlite3 /home/weewx/archive/weewx.sdb
sqlite> delete from archive where dateTime > X;
sqlite> .exit
```

The timestamp `X` is the current time in unix epoch time (number of seconds since 1 January 1970).
The website [www.epochconverter.com](https://www.epochconverter.com/) is useful for determining the
current unix epoch time.

### Corrupt station memory
If you have a Vantage station, the problem might be because the data on board your console has
become garbled. The way the Davis Vantage series works is that the software (WeeWX in this case)
asks the console for all archive data "since" some time. The console then downloads the records one
at a time. After it gets to the very last one, the memory wraps around, and the timestamp will
suddenly jump backwards in time a couple weeks — this is how the software knows it has downloaded
the last record and so it stops.

However, if the internal memory gets garbled, the console will immediately return archives in the
past, and so it looks like the timestamps have decreased in value and so WeeWX figures that is it:
there is no more data. Here is what the log typically looks like (with `debug=1`):

```
Nov 26 14:20:15 raspberrypi weewx[3849]: vantage: Getting archive packets since 2016-11-25 19:55:00 CET (1480100100)
Nov 26 14:20:15 raspberrypi weewx[3849]: vantage: gentle wake up of console successful
Nov 26 14:20:15 raspberrypi weewx[3849]: vantage: Retrieving 45 page(s); starting index= 1
Nov 26 14:20:15 raspberrypi weewx[3849]: vantage: DMPAFT complete: page timestamp 2016-11-02 19:25:00 CET (1478111100) less than final timestamp 2016-11-25 19:55:00 CET (1480100100)
Nov 26 14:20:15 raspberrypi weewx[3849]: vantage: Catch up complete.
Nov 26 14:20:15 raspberrypi weewx[3849]: reportengine: Running reports for latest time in the database.
Nov 26 14:20:15 raspberrypi weewx[3849]: reportengine: Running report StandardReport
Nov 26 14:20:15 raspberrypi weewx[3849]: vantage: Requesting 200 LOOP packets.
Nov 26 14:20:15 raspberrypi weewx[3849]: reportengine: Found configuration file /etc/weewx/skins/Standard/skin.conf for report StandardReport
Nov 26 14:20:15 raspberrypi weewx[3849]: cheetahgenerator: using search list ['weewx.cheetahgenerator.Almanac', 'weewx.cheetahgenerator.Station', 'weewx.cheetahgenerator.Stats', 'weewx.cheetahgenerator.UnitInfo', 'weewx.cheetahgenerator.Extras']
Nov 26 14:20:15 raspberrypi weewx[3849]: vantage: gentle wake up of console successful
Nov 26 14:20:19 raspberrypi weewx[3849]: cheetahgenerator: Generated 14 files for report StandardReport in 4.16 seconds
Nov 26 14:20:20 raspberrypi weewx[3849]: genimages: Generated 12 images for StandardReport in 0.62 seconds
Nov 26 14:20:20 raspberrypi weewx[3849]: reportengine: copied 9 files to /var/www/html/weewx
Nov 26 14:20:20 raspberrypi weewx[3849]: reportengine: Running report FTP
Nov 26 14:20:20 raspberrypi weewx[3849]: reportengine: Found configuration file /etc/weewx/skins/Ftp/skin.conf for report FTP
```

Note how WeeWX did a `DMPAFT` command ("`Getting archive packets since 2016-11-25 19:55:00`"). The
console responded that it has lots of records after that time ("`Retrieving 45 page(s); starting
index= 1`"), but when WeeWX actually tries to retrieve them, they are all before the requested
time.

There seems to be several fixes:

1. Unplug the console, take out the batteries, and wait a minute or two. This will cause the
console software to internally reboot. In one case this has fixed the problem without data loss.

2. If all else fails, stop WeeWX if it is running and use the utility `wee_device` to first dump any useful data in the logger,
then to clear the memory.

        wee_device --dump
        wee_device --clear-memory

    Note that the `--dump` command can generate lots of "duplicate primary key" errors. These can be
ignored.

3. If the `--clear-memory` option fails, then the archive memory of your data logger may have gone bad.  You may need to replace the data logger hardware. As a work-around, you can disable the usage of the archive by setting option `record_generation` to `software` in the configuration file `weewx.conf`:

    ```ini
    [StdArchive]
        ...
        record_generation = software
    ```

## 3rd party Vantage connectors
This section is for those who are using a homebrew or 3rd party connector to a Davis Vantage
console that does not contain a logger, such as the
[DSI-01](http://www.wxforum.net/index.php?topic=14063.0) serial interface. That is, it is a pure
serial connection to the console, with no onboard memory.

For these interfaces, you must set record generation to software. Without this information, WeeWX
is unable to detect the absence of onboard memory. If you do not do this, you will get errors that
look like the following in your system log:

```
Nov 27 20:30:21 rpi weewx[5607]: reportengine: Caught unrecoverable exception in generator weewx.filegenerator.FileGenerator
Nov 27 20:30:21 rpi weewx[5607]:         ****  'NoneType' object has no attribute '__getitem__'
Nov 27 20:30:21 rpi weewx[5607]:         ****  Traceback (most recent call last):
Nov 27 20:30:21 rpi weewx[5607]:         ****    File "/home/weewx/bin/weewx/reportengine.py", line 132, in run
Nov 27 20:30:21 rpi weewx[5607]:         ****      obj.start()
Nov 27 20:30:21 rpi weewx[5607]:         ****    File "/home/weewx/bin/weewx/reportengine.py", line 259, in start
Nov 27 20:30:21 rpi weewx[5607]:         ****      self.run()
Nov 27 20:30:21 rpi weewx[5607]:         ****    File "/home/weewx/bin/weewx/filegenerator.py", line 41, in run
Nov 27 20:30:21 rpi weewx[5607]:         ****      self.setup()
Nov 27 20:30:21 rpi weewx[5607]:         ****    File "/home/weewx/bin/weewx/filegenerator.py", line 52, in setup
Nov 27 20:30:21 rpi weewx[5607]:         ****      self.initAlmanac(self.gen_ts)
Nov 27 20:30:21 rpi weewx[5607]:         ****    File "/home/weewx/bin/weewx/filegenerator.py", line 87, in initAlmanac
Nov 27 20:30:21 rpi weewx[5607]:         ****      rec = self.getRecord(archivedb, celestial_ts)
Nov 27 20:30:21 rpi weewx[5607]:         ****    File "/home/weewx/bin/weewx/filegenerator.py", line 115, in getRecord
Nov 27 20:30:21 rpi weewx[5607]:         ****      record_dict_vt = weewx.units.dictFromStd(record_dict)
Nov 27 20:30:21 rpi weewx[5607]:         ****    File "/home/weewx/bin/weewx/units.py", line 892, in dictFromStd
Nov 27 20:30:21 rpi weewx[5607]:         ****      std_unit_system = d['usUnits']
Nov 27 20:30:21 rpi weewx[5607]:         ****  TypeError: 'NoneType' object has no attribute '__getitem__'
Nov 27 20:30:21 rpi weewx[5607]:         ****  Generator terminated...
Nov 27 20:30:23 rpi weewx[5607]: genimages: Generated 11 images in 2.53 seconds
See the section on option record_generation.
```

### Many LOOP read errors

The symptom is many LOOP errors and unreliable downloading of archive records. Your log may look
like this:

```
Jan 18 20:38:52 rpi weewx[6024]: VantagePro: Opened up serial port /dev/ttyUSB0, baudrate 19200
Jan 18 20:38:53 rpi weewx[5977]: VantagePro: LOOP #12; read error. Try #1
Jan 18 20:38:53 rpi weewx[5977]:       ****  Expected to read 99 chars; got 0 instead
Jan 18 20:38:58 rpi weewx[7543]: VantagePro: LOOP #13; read error. Try #1
Jan 18 20:38:58 rpi weewx[7543]:       ****  Expected to read 99 chars; got 4 instead
Jan 18 20:39:03 rpi weewx[7543]: VantagePro: LOOP #14; read error. Try #2
Jan 18 20:39:03 rpi weewx[7543]:       ****  Expected to read 99 chars; got 0 instead
Jan 18 20:39:03 rpi weewx[5977]: VantagePro: LOOP #13; read error. Try #2
Jan 18 20:39:03 rpi weewx[5977]:       ****  Expected to read 99 chars; got 4 instead
Jan 18 20:39:08 rpi weewx[7543]: VantagePro: LOOP #15; read error. Try #3
Jan 18 20:39:08 rpi weewx[7543]:       ****  Expected to read 99 chars; got 4 instead
Jan 18 20:39:09 rpi weewx[5977]: VantagePro: LOOP #14; read error. Try #3
Jan 18 20:39:09 rpi weewx[5977]:       ****  Expected to read 99 chars; got 2 instead
Jan 18 20:39:14 rpi weewx[5977]: VantagePro: LOOP #15; read error. Try #4
Jan 18 20:39:14 rpi weewx[5977]:       ****  Expected to read 99 chars; got 2 instead</pre>
```

If you look closely at the log above, you'll see that there are multiple instances of WeeWX running
simultaneously (process IDs `5977`, `6024`, and `7543`). They are competing with each other for
control of the console, resulting in missed packets and records.

The cure is simple: kill all but one of them. Or, better yet, kill them all, then restart WeeWX.


### Console in setup mode

Be sure that the VantagePro2 console is not in setup mode!

Sometimes after you power-cycle the console, it stays in setup mode until you do a long press on the 'Done' button.  While the console is in setup mode, WeeWX can get station information (for example, `wee_device --info`) and it can even clear the station memory (`wee_device --clear-memory`).  But as long as the console is in setup mode, when WeeWX tries to get data it will fail, reporting messages such as these:

```
Mar 17 16:39:43 sailing weewx[25608]: engine: Starting main packet loop.
Mar 17 16:39:48 sailing weewx[25608]: vantage: LOOP try #1; error: Expected to read 99 chars; got 0 instead
Mar 17 16:39:53 sailing weewx[25608]: vantage: LOOP try #2; error: Expected to read 99 chars; got 0 instead
Mar 17 16:39:58 sailing weewx[25608]: vantage: LOOP try #3; error: Expected to read 99 chars; got 0 instead
Mar 17 16:40:03 sailing weewx[25608]: vantage: LOOP try #4; error: Expected to read 99 chars; got 0 instead
Mar 17 16:40:03 sailing weewx[25608]: vantage: LOOP max tries (4) exceeded.
Mar 17 16:40:03 sailing weewx[25608]: engine: Caught WeeWxIOError: Max tries exceeded while getting LOOP data.
Mar 17 16:40:03 sailing weewx[25608]:     ****  Waiting 60 seconds then retrying...
```
