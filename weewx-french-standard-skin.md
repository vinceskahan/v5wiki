This skin is a french translation for the weewx 3.5.0 default Standard skin that is available with weewx by default.
This skin should work with weewx 3.3.0 or upper.

###Download

https://github.com/weewx/weewx/files/293536/Standard-FR-v1.0.tar.gz

###Installation

1. Stop weewx process:

    ```
sudo /etc/init.d/weewx stop
```

2. Get the file :

    ```
cd /etc/weewx/skins/
wget https://github.com/weewx/weewx/files/293536/Standard-FR-v1.0.tar.gz
mv Standard Stardard-ORI
tar xzvf Standard-FR-v1.0.tar.gz
mv Standard-FR Standard
chown root:root Standard

```

3. Enable the weather radar picture (optionnal):

```
cd Standard
Edit the file skin.conf and uncomment the lines:
    # radar_img = http://37.59.123.0/sat/anim-msg-fr-vis.gif
    # radar_url = http://fr.sat24.com/fr/fr/visual

    ```

4. Configure the good metric units for the french weewx skin:

Part 1:Edit the file /etc/weewx/weewx.conf 
Not sure, but you need to have 
target_unit = METRIC
or
target_unit = METRICWX
 
Part 2:Edit the file /etc/weewx/weewx.conf and replaces the lines:

[[[Units]]]
            [[[[Groups]]]]
                group_altitude = meter
                group_speed2 = meter_per_second2
                group_pressure = mbar
                group_rain = mm
                group_rainrate = mm_per_hour
                group_temperature = degree_C
                group_degree_day = degree_C_day
                group_speed = meter_per_second
by

        [[[Units]]]
            [[[[Groups]]]]
                group_altitude = meter
                group_speed2 = km_per_hour2
                group_pressure = hPa
                group_rain = mm
                group_rainrate = mm_per_hour
                group_temperature = degree_C
                group_degree_day = degree_C_day
                group_speed = km_per_hour

6. Start weewx process:

    ```
sudo /etc/init.d/weewx stop
```

7. Go to your weewx URL home page and check the home page.