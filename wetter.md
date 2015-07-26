### wetter.com

Lebe das Wetter – das ist das Motto des führenden deutschsprachigen Wetterportal. Aber das Produkt- portfolio von wetter.com umfasst viel mehr als unsere Portalseiten und unsere Apps. Die wetter.com AG betreibt zudem den einzigen 24 Stunden Wetterkanal Deutschlands und beliefert eine Vielzahl von reno- mierten TV, Radio- und Printmedien mit Wetterinformationen.

This is an extension to weewx that uploads weather data to wetter.com.

### Download

http://lancet.mit.edu/mwall/projects/weather/releases/weewx-wetter-0.3.tgz

### How to Install

1.  Run the extension installer:

    ```
wee_extension --install weewx-wetter-x.y.tgz
```

2.  Modify weewx.conf:

    ```
    [StdRESTful]
        [[Wetter]]
            # User-Id/Kennwort für die automatische Meldung von Wetterdaten, nicht zum login wetter.com
            username = USERNAME
            password = PASSWORD
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