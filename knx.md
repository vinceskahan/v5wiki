### Write EIBnet/IP packets

This weewx[1] extension allows the generation of EIBnet/IP packets
containing weather information collected by weewx.
This extension was written for the purpose of easy integration with EIB home automation.

### Download

wget -O weewx-knx.zip https://github.com/AZAZ78/weewx-knx/archive/main.zip

### How to Install
1. Install pknx (knxip)
...
Follow installation instructions for https://github.com/open-homeautomation/pknx
...

1.  Run the extension installer

```
wee_extension --install weewx-knx.zip
```

2.  Restart weewx

```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```

### Verify

knx service sends data to bus at every archive interval.  Look at the bus monitor to see data.

### Customize
Adapt [KNX] configuration in weewx config file.
Example:
   # EIBnet/IP gateway configuration
      gateway_ip = 192.168.2.70
      gateway_port = 3671

   # node configuration
      outTemp = 5.5.150
      wind = 5.5.151
      windGust = 5.5.152
      rainRate = 5.5.153
      outHumidity = 5.5.154
