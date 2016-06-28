This skin is a french translation for the weewx 3.5.0 default Standard skin that is available with weewx by default.
Only add a new graph/pictures for humidity inside and outside because this information is needed for farming. 
This skin should work with weewx 3.3.0 or upper.

###Download

https://github.com/weewx/weewx/files/338169/Standard-FR-v1.0.1.tar.gz

###Installation

1. Stop weewx process:

    ```
sudo /etc/init.d/weewx stop
```

2. Get the file :

    ```
cd /etc/weewx/skins/
wget https://github.com/weewx/weewx/files/293536/Standard-FR-v1.0.1.tar.gz
tar xzvf Standard-FR-v1.0.1.tar.gz
chown root:root Standard-FR
```

3. Enable the weather radar picture (optionnal):

    ```
cd Standard-FR
By default cloud satellite pictures from meteo60.fr are enabled.
If you want cloud satellite pictures from sat24.com, edit the file skin.conf and uncomment the lines:
    # radar_img = http://37.59.123.0/sat/anim-msg-fr-vis.gif
    # radar_url = http://fr.sat24.com/fr/fr/visual
and comment the lines :
    radar_img = http://www.meteo60.fr/satellites/animation-satellite-visible-france.gif
    radar_url = http://www.meteo60.fr/sat_vis.php
```

4. Configure the good skin and metric units for the french weewx skin:

Part 1:Edit the file /etc/weewx/weewx.conf, and in the "StandardReport" part, update the parameter

 skin = Standard <BR>
by <BR>
 skin = Standard-FR
 
Part 2:Edit the file /etc/weewx/weewx.conf and replaces the lines:

    ```
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
```
by
    ```

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
```

6. Start weewx process:

    ```
sudo /etc/init.d/weewx start
```

7. Go to your weewx URL home page and check the home page.

###Screenshots
![weewx-french-screenshot-2](https://cloud.githubusercontent.com/assets/446723/15724786/f4bad8ec-2848-11e6-82b4-f9b9336e78bb.png)