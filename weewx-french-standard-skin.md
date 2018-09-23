This skin is a french translation of the weeWX default Standard skin. 

### Download

French translation of the weeWX v3.5.0 default Standard skin (This skin should work with weewx 3.3.0 or later):
https://github.com/weewx/weewx/files/338169/Standard-FR-v1.0.1.tar.gz

French translation of the weeWX v3.8.2 default Standard skin 
https://github.com/weewx/weewx/files/2409004/weeWX_v3.8.2-Standard-FR.tar.gz

### Installation

1. Stop the weeWX process:

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

3. Enable the weather radar picture (optional). By default, cloud satellite pictures 
from meteo60.fr are enabled. If you want cloud satellite pictures from sat24.com, edit the file `skin.conf` and uncomment the lines:
```
    # radar_img = http://37.59.123.0/sat/anim-msg-fr-vis.gif
    # radar_url = http://fr.sat24.com/fr/fr/visual
```
and comment the lines :
```
    radar_img = http://www.meteo60.fr/satellites/animation-satellite-visible-france.gif
    radar_url = http://www.meteo60.fr/sat_vis.php
```
4. Configure weeWX to use the French skin. Edit the file `weewx.conf`, and in the `[[StandardReport]]` section, change

```
 skin = Standard
```
to
```
 skin = Standard-FR
```

5. Start weewx process:

```
sudo /etc/init.d/weewx start
```

6. Go to your weewx URL home page and check the home page.

### Screenshots
![weewx-french-screenshot-2](https://cloud.githubusercontent.com/assets/446723/15724786/f4bad8ec-2848-11e6-82b4-f9b9336e78bb.png)