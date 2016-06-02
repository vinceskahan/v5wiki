## Aercus Instruments WeatherSleuth
### WeeWX Setup using the Interceptor driver

**A few important things to note about this weather station:**  

Designed for internet/network use only - it does not feature a display console.  
Wireless means wireless reception from the weather sensors. **It does NOT have WiFi.**  
Connection to your router/network is via ethernet.  

It is essentially a weather station to ethernet bridge, and shares commonality with some other brands although it seems the firmware varies across various brands & models as to how easy or difficult it is to integrate with WeeWX - accomplished by changing the default upload site from \(usually\) Weather Underground or capturing data packets.  

In the case of the Aercus WeatherSleuth, the firmware allows you to easily specify a custom location to send the data.  

### The weather station  

Manufacturers website: http://www.aercusinstruments.com  
Manufacturers website, direct link to the weather station: http://www.aercusinstruments.com/copy-of-aercus-instruments-weatherranger-professional-weather-station-with-wifi-and-real-time-internet-publishing  
Purchasable in the UK from Greenfrog Scientific: http://www.greenfrogscientific.co.uk/aercus-instruments-weathersleuth  
And Amazon UK (mine was, in fact, despatched from Greenfrog) http://www.aercusinstruments.com/copy-of-aercus-instruments-weatherranger-professional-weather-station-with-wifi-and-real-time-internet-publishing  

The outdoor array has the usual sensors (wind direction, wind speed, rain, temperature, humidity) + UV & Solar Radiation.  

Meanwhile the indoor sensor has temperature, pressure & humidity - indoor humidity is not recorded by WeeWX as a default but can be added to the reports if desired.  

The receiver base station is a small box which has an aerial for receiving the data from the outdoor & indoor sensors, an RJ-45 ethernet port & a power socket for the supplied 5vDC supply.
It does not feature WiFi, it must be physically plugged into using an ethernet cable, which is also supplied.  

There are a variety of status lights on the receiver, which incidentally is tiny - fits in the palm of your hand - and since it doesn't display any weather info \(there is no console display\) it makes it ideal if you just want to feed data into WeeWX and not bother with USB cables from desktop weather stations etc.  

The default set-up out of the box is feeding to Weather Underground, but this is one of the IP-based systems where the data feed destination can be easily changed.  

Mine was shipped with Firmware 2.1.9, which is the latest.  

Out of the box it comes set for DHCP, so you will have to dive into your router or use some network scanning software to find it.  

Alternatively there is a Windows utility supplied on the tiny CD **\(don't slot that in your laptop or any other slot-loading drive, it will wreck the drive\)**  

Since I'm a Mac & Linux user I didn't use the windows utility but instead logged into my router and found the device - fairly easy since the MAC/hardware address is printed on a label stuck on the bottom of the receiver.  

In my case I set a Static DHCP lease in the router, but alternatively you may want to just set a static IP address within the weather station itself or just leave it DHCP, see Local Network config below.  
Once you have the IP, open up a web browser and head to the weather station's IP address config page:  

http://ip.address.of.the.weathersleuth.  
eg: http://192.168.1.124  if thats the address your weather station has picked up from your router.  

There you'll see the config tabs, the first one speaks for itself **Local Network** - DHCP or manual IP, plus fields for the router IP, DNS, Subnet etc. If you're happy to find it on DHCP if & when you need to change anything I don't see any reason not to leave it set to DHCP, as it pushes data to weewx and not the other way around.  

Next tab is for the **Weather Network**, which is where we will set it up to feed to your weewx system.
From the Remote Server pull-down menu, choose *Customized*  

In the *Server/Hostname* box, type in the IP address of the computer you're running WeeWX on. In my case, its 192.168.45.100  

I don't know, and haven't tried, whether you could supply a local named address such as "weewx.local" if your computer is called that. The only reason you might want to do this is if the computer running WeeWX is using DHCP - I run WeeWX on a Ubuntu server which also does other stuff, so its on a static address on my network.  

In the *Server Port* box, specify a unique TCP/IP port that WeeWX or other software on the computer isn't using. Try not to use standard ports even if you're not using them right now - you may in the future.
I chose 55, which might be a mistake further down the line, but it can always be changed - with the relevant change in weewx.conf to match.  

In the *Server Type* pull-down menu, choose *PHP* - note that the other options, JSP & ASP, didn't seem to provide accurate data in my case.  

In the *Station ID* box enter something meaningful. This isn't required for WeeWX, but the weather station needs something there. I used: "weather"  

In the *Password* box enter something this isn't a real password you use, I used "password" - again it has to be there, but it also gets included in the PHP data which is sent to WeeWX in plain text. If you have to paste the output for diagnostics anywhere, such as WeeWX forums, you don't want your most secret of secret password there for all to see!  

**Station Settings**  
There are sections for indoor and outdoor sensor types. I enquired with Aercus about extra sensors but it seems there are none available yet.  
Leave these at default :  
*Indoor Sensor Type: WH25* \(options: WH25, None\)  
*Outdoor Sensor1 Type: WH24* \(options: WH24, WH7, None\)  
*Outdoor Sensor2 Type: None* \(options: WH26, None\)  
*Wireless Receive Frequency: 434* \(not changeable and greyed out, refers to the wireless sensor frequency: 434MHz\)  
*Time Zone setting:* as your time zone dictates  
*Daylight savings \(DST\): auto*  

**Units of measure**  
These only seem to affect the weather station's own internal web display, and not those sent to WeeWX - I confirmed this by changing the setting and then observing the data it sends out (see below) but if anyone wants to test this and confirm it could be useful. In my case I changed the units of measure to match those I use in weewx, but I don't think this is needed.  

**Automatic Restart:**  
*System Reboot: YES*  
I have set this to yes. It says it will automatically restart the WeatherSleuth station if it can't reach the server for 20 minutes. I guess if it tries but can't reach the server \(in this case, WeeWX\) listening on the IP address & port you chose earlier then it will restart itself \(the WeatherSleuth station, not WeeWX or the computer its running on\) - looks like the timeout isn't configurable.  

In the next tab, you'll find **Live Data**  
Here is a handy hint I picked up somewhere: Test your sensors indoors before mounting the station outside only to find something isn't working.  

So one by one, check everything: blow on the wind speed cups fairly hard a few times and observe whether wind speed is measured in the Live Data tab.  
Do the same for wind direction - move the direction vane slowly around and check it shows the relevant wind directions \(in this case, shown in degrees\)  
Temperature indoor, outdoor, humidity etc should all be showing something. Put the indoor temperature somewhere warmer or colder - does it change?  
Rain: do this over a sink or small bowel. Tip some water into the rain sensor, when it empties \(hence the sink/bowel\) out of the bottom does anything show in the rain fields?  
You could also place it gently outdoor for 5 or 10 mins to check solar radiation, UV and UV Index are all measuring but at this point I decided I was good to go and mounted it on the pole.  

In the final tab, **Calibration** I left at defaults and clicked the *Default* button, followed by *Apply* just to be sure.  

### WeeWX  

Make sure your computer is receiving data from the weather station, as WeeWX will listen for data coming in on your chosen port, Open up a terminal and type:  

```
sudo nc -l 55
```

Where 55 is the port we entered earlier in the WeatherSleuth setup. If you chose a different port, use that.  

Wait for a few minutes and some stuff should appear, here is an example from my setup: You will see the ID=weather&PASSWORD=password. Remember we entered that into the weather station earlier, this is why you don't want to use anything sensitive. The host 192.168.45.100 is the IP address of my computer running WeeWX, which we also specified earlier when we set up the WeatherSleuth. In you case, of course, it will be different.  

```
GET /weatherstation/updateweatherstation.php?ID=weather&PASSWORD=password&tempf=39.7&humidity=86&dewptf=36.0&windchillf=39.7&winddir=255&windspeedmph=0.00&windgustmph=0.00&rainin=0.00&dailyrainin=0.00&weeklyrainin=0.00&monthlyrainin=0.00&yearlyrainin=0.00&solarradiation=0.00&UV=0&indoortempf=67.8&indoorhumidity=46&baromin=30.08&lowbatt=0&dateutc=2016-4-30%2022:7:9&softwaretype=Weather%20logger%20V2.1.9&action=updateraw&realtime=1&rtfreq=5 HTTP/1.0
Accept: */*
Host: 192.168.45.100
Connection: Close
```

If all looks well then install the interceptor driver in WeeWX:  

Instructions are here:  https://github.com/matthewwall/weewx-interceptor  

Follow the instructions on downloading, installing, and configuring in weewx.  

### weewx.conf  

Here is the relevant part of my weewx.conf  
I'm using the Python-installer version so mine is here:  

```
/home/weewx/weewx.conf 
```

```
[Interceptor]
    # This section is for the network traffic interceptor driver.
    # Specify the hardware device to capture.  Options include:
    #   acurite-bridge - acurite internet bridge
    #   observer - fine offset WH2600/HP1000/HP1003, aka 'observer'
    #   lw30x - oregon scientific LW301/LW302
    #   lacross-bridge - lacross GW1000U/C84612 internet bridge
    #   netatmo - netatmo weather stations

    device_type = observer
    port = 55

    # The driver to use:

    driver = user.interceptor
```

And in my case, I modify the ```[StdReport]``` section to reflect my own tastes, including wind speed in knots being a bit of a plane geek \(plane spotter!\)  

```
[StdReport]

    # Where the skins reside, relative to WEEWX_ROOT
    SKIN_ROOT = skins

    # Where the generated reports should go, relative to WEEWX_ROOT
    HTML_ROOT = public_html

    # The database binding indicates which data should be used in reports.
    data_binding = wx_binding

    # Each of the following subsections defines a report that will be run.

    [[StandardReport]]

        # See the customizing guide to change the units, plot types and line
        # colors, modify the fonts, display additional sensor data, and other
        # customizations. Many of those changes can be made here by overriding
        # parameters, or by modifying templates within the skin itself.
    
        # The StandardReport uses the 'Standard' skin, which contains the
        # images, templates and plots for the report.

        skin = Standard
        [[[Units]]]
            [[[[Groups]]]]
                group_altitude = meter
                group_speed2 = knot2
                group_pressure = hPa
                group_rain = mm
                group_rainrate = mm_per_hour
                group_temperature = degree_C
                group_degree_day = degree_C_day
                group_speed = knot

```

That should be about it. By default it seems to update weewx every 5 mins or so. I expect this is configurable should that be too much, too often, either in the Interceptor driver options and/or elsewhere within WeeWX.  