# Collect data from the ecowitt GW1000 wifi bridge

This guide shows how to configure weeWX to collect data from the ecowitt GW1000 wifi bridge.  The GW1000 is manufactured by Fine Offset.  It receives 915MHz radio signals from a wide assortment of Fine Offset sensors, then sends the data via WiFi to various online services.  The bridge itself is small, powered by USB, and has no interface other than a single button and two LEDs.  It includes pressure, temperature, and humidity sensor at the end of an attached 1 meter wire.

## What does it look like?

<img src="gw1000-recipe/gw1000-front.JPG" width="200">
<img src="gw1000-recipe/gw1000-side.JPG" width="200">

## What do you need?

At the very least you need a GW1000.  Then you can add any number of sensors.

| cost | description | example model | source |
|---|---|---|---|
| $20 | GW1000 | ecowitt | https://www.amazon.com/ECOWITT-Gateway-Temperature-Humidity-Pressure/dp/B07JLRFG24 |

The prices are US$ as of December 2019.

## Recipes

### Configure the GW1000

Follow the instructions that came with the GW1000.  That basically boils down to this:

1. Plug the GW1000 into a USB port.  The GW1000 only uses the USB port for power.

2. Install the WSView app on a phone.

3. Using the WSView app, connect to the GW1000 and configure it to use the local WiFi network.

If those steps were successful, you should see live temperature, humidity, and pressure data from the GW1000.

The GW1000 will immediately start trying to send data to a server at ecowitt.net, at a frequency of about one attempt per minute.  If the WiFi network is connected to the internet, the ecowitt.net server will reject the request with a response like this:

```
{"errcode":"40001","errmsg":"invalid passkey"}
```

If you happen to get an older GW1000, then the WSView app might prompt you immediately to upgrade the firmware on the GW1000.  You probably want firmware at least v1.5.5, since by then support for a wide variety of sensors had been added.  Firmware 1.4 was pretty anemic.

### Install weeWX

When you install weeWX, select `Simulator` when prompted for the station type.  You will change it later to `Interceptor` when you run the `wee_config --reconfigure` command.
```
# install weeWX
wget -qO - http://weewx.com/keys.html | sudo apt-key add -
wget -qO - http://weewx.com/apt/weewx.list | sudo tee /etc/apt/sources.list.d/weewx.list
sudo apt-get update
sudo apt-get install weewx

# shut down weeWX
sudo /etc/init.d/weewx stop

# install weewx-sdr extension and enable the driver
git clone https://github.com/matthewwall/weewx-interceptor.git
sudo wee_extension --install weewx-interceptor
sudo wee_config --reconfigure
```

### Configure the interceptor

#### Option 1: DNS hijack

The easiest way to capture data is to 

