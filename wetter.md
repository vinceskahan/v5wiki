### wetter.com

Lebe das Wetter – das ist das Motto des führenden deutschsprachigen Wetterportal. Aber das Produkt- portfolio von wetter.com umfasst viel mehr als unsere Portalseiten und unsere Apps. Die wetter.com AG betreibt zudem den einzigen 24 Stunden Wetterkanal Deutschlands und beliefert eine Vielzahl von reno- mierten TV, Radio- und Printmedien mit Wetterinformationen.

This is an extension to weewx that uploads weather data to wetter.com.

### Download

wget -O weewx-wetter.zip https://github.com/matthewwall/weewx-wetter/archive/master.zip

### How to Install

1.  Run the extension installer:

```
wee_extension --install weewx-wetter.zip
```

2.  Modify weewx.conf:

```
[StdRESTful]
    [[Wetter]]
        username = STATION ID                                                   
        password = STATION PASSWORD
```

3.  Restart weewx

```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Upgrading from weewx 2.6-2.7

Simply run the extension installer then restart weewx.  If your weewx.conf already contained a wetter username and password, these should be remembered by the installer.

### Upgrading from weewx 2.5

If you were using the wetter extension with weewx 2.5.x, be sure to remove the 'driver' parameter from the Wetter section in weewx.conf.