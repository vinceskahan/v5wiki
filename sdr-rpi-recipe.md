# Use software-defined radio (SDR) to collect data

This is a guide to build a low-cost system that will collect data from any number of wireless sensors.  The recipes in this guide use a USB software-defined radio (SDR) plugged in to a raspberry pi, collecting data from Acurite temperature and temperature/humidity sensors.  This approach will work with many other types of sensors from other manufacturers, and it will run on other types of computers and operating systems.

Each sensor uses 2 AA batteries, and the batteries typically last a couple of years under normal temperature ranges.  The raspberry pi has a real-time clock and is connected to a home router via ethernet (an old ASUS running tomatoUSB).  A network connection is not required to run the system, but it is necessary to download the software.

It took me about 2 hours to build this system.  That includes the time to remove all of the hardware from its packaging, place the sensors around the house, take the dog for a walk, and take a short break to eat some freshly baked cookies.

## What does it look like?

<img src="sdr-rpi-recipe/sdr-and-rpi.png" width="200">
<img src="sdr-rpi-recipe/sensors.png" width="200">
<img src="sdr-rpi-recipe/sensor-model-info.png" width="200">

## What do you need?

| cost | description | example model | source |
|---|---|---|---|
| $20 | USB SDR | NooElec | https://www.amazon.com/NooElec-NESDR-Mini-Compatible-Packages/dp/B009U7WZCA/ |
| $40 | RPi | 3B+ | https://www.amazon.com/ELEMENT-Element14-Raspberry-Pi-Motherboard/dp/B07BDR5PDW/ |
| $12 | SD card | SanDisk Ultra 64 | https://www.amazon.com/dp/B073JYVKNX/ |
| $6 | Case | | https://www.amazon.com/Raspberry-Case-Black-Compatible-Model/dp/B00UW2G1BS/ |
| $10 | Power supply | | https://www.amazon.com/CanaKit-Raspberry-Supply-Adapter-Listed/dp/B00MARDJZ4/ |
| $13 | temperature/humidity sensor | Acurite 06002M | https://www.amazon.com/AcuRite-06002M-Wireless-Temperature-Humidity/dp/B00T0K8NXC/ |
| $13 | temperature sensor | Acurite 606TX |https://www.amazon.com/AcuRite-606TX-Wireless-Temperature-Sensor/dp/B00V4R5EUO/ |
| $13 | temperature/humidity sensor | FineOffset WH31 | https://www.amazon.com/ECOWITT-Multi-Channel-Temperature-Humidity-Sensor/dp/B07JLRJRLM/ |

The prices are US$ as of January 2019.  I have seen the temperature sensors on sale at Walmart for as little as $5 each.

Beware of the Acurite 606TX!  The identifier for that sensor changes when the sensor is power-cycled.  So if you use that sensor, you will have to reconfigure WeeWX each time you change the batteries.

## Recipes

### Configure the raspberry pi

There are many guides available for bootstrapping a raspberry pi.  There are also many guides available for installing and configuring a real-time clock in the raspberry pi.  Do it!

The rest of this guide requires only command-line access to the pi.  So you can do the steps below remotely logged in via ssh, or in a terminal window with a keyboard and monitor plugged in to the pi.

```
# install operating system on sdcard, use it to boot the rpi

# install a real-time clock and enable it

# configure the rpi for remote access and headless operation

# get rid of fake clock
sudo apt-get remove --purge fake-hwclock

# ensure the correct timezone
sudo dpkg-reconfigure tzdata
```

### Install pre-requisites for building rtl-sdr and rtl_433
```
sudo apt-get install cmake libusb-1.0-0-dev build-essential autoconf libtool pkg-config
```

### Install rtl-sdr
```
git clone git://git.osmocom.org/rtl-sdr.git
cd rtl-sdr
mkdir build
cd build
cmake -DINSTALL_UDEV_RULES=ON ..
make
sudo make install
sudo ldconfig
```

### Install rtl_433
```
git clone https://github.com/merbanan/rtl_433
cd rtl_433
mkdir build
cd build
cmake ..
make
sudo make install
```

### Install weeWX

When you install weeWX, select `Simulator` when prompted for the station type.  You will change it later to `SDR` when you run the `wee_config --reconfigure` command.
```
# install weeWX
wget -qO - http://weewx.com/keys.html | sudo apt-key add -
wget -qO - http://weewx.com/apt/weewx.list | sudo tee /etc/apt/sources.list.d/weewx.list
sudo apt-get update
sudo apt-get install weewx

# shut down weeWX
sudo /etc/init.d/weewx stop

# install weewx-sdr extension and enable the driver
git clone https://github.com/matthewwall/weewx-sdr.git
sudo wee_extension --install weewx-sdr
sudo wee_config --reconfigure
```

### Configure

Now that all the pieces are installed, it is time to tell weeWX which data to collect.  This is done by starting at the lowest level, `rtl_433`, then working up to `weewxd`.  You will first run `rtl_433` to verify that it works and to see what signals it picks up.  You might be surprised by how many devices in your house (or your neighbors' house!) are sending radio signals that you can detect.  Then the next step is to identify which of those signals you care about.  Finally, you will create a `sensor_map` in your weeWX configuration that maps names and values from `rtl_433` into the database fields that are used in `weeWX`.

```
# see what devices are broadcasting data - let this run for awhile
sudo rtl_433

# or run it like this to see the output in JSON format
sudo rtl_433 -M utc -F json
```

Deploy the sensors one at a time.  Put the batteries into the first sensor, then watch it show up in the `rtl_433` output.  Put a piece of tape on the sensor then label that sensor with the hardware identifier - the hardware identifier is typically called `id` in the output.  Then put batteries in the next sensor, and watch it show up.  You will end up with a pile of sensors, each with its hardware identifier clearly marked.  Then you can easily keep track of sensors when you map the hardware identifiers to the database fields and the actual sensor locations.

Next you should run the `weewx-sdr` driver directly.  This will verify that the driver can capture the output from `rtl_433`, and it will show you the fully-qualified names for each observation.  You will need these to tell weeWX how to capture the data.  Notice the `--cmd` option.  If you had to specify other options to `rtl_433`, be sure to add those to the `--cmd` option when you invoke `sdr.py`.
 
```
# see how the sensor data from rtl_433 are mapped to fully-qualified names
sudo PYTHONPATH=/usr/share/weewx python /usr/share/weewx/user/sdr.py --cmd="rtl_433 -M utc -F json"

# in particular, look at the 'out' and 'parsed' lines like this:
out: ['{"time" : "2019-01-16 11:45:33", "model" : "Acurite tower sensor", "id" : 2453, "sensor_id" : 2453, "channel" : "A", "temperature_C" : 16.700, "humidity" : 31, "battery_low" : 0}\n']
parsed: {'temperature.0995.AcuriteTowerPacket': 16.7, 'dateTime': 1547639133, 'humidity.0995.AcuriteTowerPacket': 31.0, 'status.0995.AcuriteTowerPacket': None, 'battery.0995.AcuriteTowerPacket': 0, 'channel.0995.AcuriteTowerPacket': u'A', 'usUnits': 16}
```

Now you can tell weeWX the full names of the observations from each sensor.  In this example, the full names are `temperature.0995.AcuriteTowerPacket` or `humidity.0995.AcuriteTowerPacket`.  You must tell weeWX how to map those names to the database fields it uses to store the data.  This is done in the `sensor_map` section of the `SDR` section in the weeWX configuration file.

Here is an example showing 4 sensors that I installed.  The sensor identifiers are `-102`, `0995`, `16B9`, and `0ED5`, but of course yours will have other values.  The database field names are `inTemp`, `outTemp`, `outHumidity`, etc.

```
# using these names, create the sensor_map in /etc/weewx/weewx.conf
[SDR]
    ...
    [[sensor_map]]
        inTemp = temperature.-102.Acurite606TXPacket # mud room
        outTemp = temperature.0995.AcuriteTowerPacket # back porch
        outHumidity = humidity.0995.AcuriteTowerPacket
        extraTemp1 = temperature.16B9.AcuriteTowerPacket # bathroom
        extraHumidity1 = humidity.16B9.AcuriteTowerPacket
        extraTemp2 = temperature.0ED5.AcuriteTowerPacket # kitchen
        extraHumidity3 = humidity.0ED5.AcuriteTowerPacket
```

You can see the full list of database field names in the [archive types](http://weewx.com/docs/customizing.htm#archive_types) section of the weeWX customization guide.

### Start weewx

First run weeWX directly to ensure that the data collection is working properly, and that data are getting into the database and reports.  A minute or two after you start it, you should see `LOOP` packets that contain the data from the sensors, associated with database field names as defined in your `sensor_map`.  Every 5 minutes you should see an archive `RECORD` reported.

```
# run weewx directly to verify the data collection (ctrl-c to stop)
weewxd /etc/weewx/weewx.conf
```

After you have verified it is working properly, kill `weewxd`.  Now you can run it as a daemon so that it will continue to run, even after you log out of the raspberry pi.
```
# run weewx as a daemon and forget about it!
sudo /etc/init.d/weewx start
```

### Viewing the data and customizing the reports

WeeWX saves the data to a sqlite database at `/var/lib/weewx.sdb` on the raspberry pi.  You can browse the data directly by invoking the sqlite command-line tool on the raspberry pi:

```
sqlite3 /var/lib/weewx.sdb
```

In its default configuration, weeWX will put data into a report located at `/var/www/html` on the raspberry pi.  If you have a keyboard and monitor plugged into the pi, you can view the report directly in any web browser on the pi.

You can view the report remotely by installing a web server on the pi, such as `nginx`, `lighttpd`, or `apache`.  I highly recommend using `nginx` or `lighttpd` on the pi, since they use significantly less memory than `apache`.

```
sudo apt-get install nginx
```

Then you can view the reports using a web browser on any computer/tablet/phone that can see the pi:
```
http://<name-or-addr-of-pi>/weewx
```

To customize the report or add other reports, see the weeWX customization guide:

http://weewx.com/docs/customizing.htm

## Troubleshooting

### kernel module already loaded

When you run `rtl_433`, you might get a warning about a kernel module already being loaded.  If so, you can try explicitly unloading the kernel module, then blacklisting it so that it is not accidentally loaded.
```shell
# ensure that the rtl kernel module is not running inappropriately
sudo modprobe -r dvb_usb_rtl28xxu
echo 'blacklist dvb_usb_rtl28xxu' | sudo tee -a /etc/modprobe.d/blacklist.conf
```

### kernel module is in use

If you get an error that `dvb_usb_rtl28xxu` is in use, unplug your SDR device, then try again.

### rtl-sdr build fails to find libusb

When compiling `rtl-sdr`, you might encounter problems linking to libusb.  If so, try running `pkg-config` as explained here:

https://stackoverflow.com/questions/65296289/fatal-error-libusb-libusb-h-no-such-file-or-directory-librtlsdr-install-for-gn/66311388#66311388
