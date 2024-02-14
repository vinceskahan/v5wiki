[RainMachine](https://rainmachine.com) is a family of smart irrigation controllers that uses weather information 
to adjust the water needs of your plants.


This is an extension to weewx that sends weather data to RainMachine. 
Latest firmware version of RainMachine is needed with API 4.6.

This extension only works on the local network (doesn't work with RainMachine 
Remote Access service). RainMachine IP address and http port 8081 or https port 8080 must be 
accessible from WeeWX installation. The usessl parameter controls if connection to RainMachine should be
http or https. On certain version of weewx the usessl=true (https) might not work because of RainMachine self-signed certificate for local network.

### Credentials

You must obtain a token which will be used for authorization when pushing the weather data to RainMachine.

```
curl -X POST -k -d ' { "pwd": "your_password", "remember":1}' https://rainmachine_ip:8080/api/4/auth/login
```

More details can be found here:.
https://support.rainmachine.com/hc/en-us/articles/228022248-Controlling-RainMachine-through-REST-API

By default data is sent to RainMachine hourly. Although is possible to send data more often the
RainMachine Mixer (that aggregates data from multiple sources) runs only hourly.

### Download

```
wget -O weewx-rainmachine.zip https://github.com/sprinkler/rainmachine-weewx/archive/master.zip
```

### How to Install

1.  Run the extension installer:

```
sudo weectl extension install weewx-rainmachine.zip
```

2.  Modify weewx.conf:

```
[StdRESTful]
    [[RainMachine]]
        token = RAINMACHINE_ACCESS_TOKEN
        ip = RAINMACHINE_IP_ADDRESS
        usessl = false

```

3.  Restart weewx

```
sudo /etc/init.d/weewx restart
```
