[RainMachine](https://rainmachine.com) is a family of smart irrigation controllers that uses weather information 
to adjust the water needs of your plants.


This is an extension to weewx that sends weather data to RainMachine. 
Latest firmware version of RainMachine is needed with API 4.6.


This extension only works on the local network (doesn't work with RainMachine 
Remote Access service). RainMachine IP address and https port 8080 must be 
accessible from WeeWX installation.


### Credentials

You must obtain a token which will be used for authorization when pushing the weather data to RainMachine.

```
curl -X POST -k -d ' { "pwd": "admin", "remember":1}' https://rainmachine_ip:8080/api/4/auth/login
```

More details can be found here:.
https://support.rainmachine.com/hc/en-us/articles/228022248-Controlling-RainMachine-through-REST-API

By default data is sent to RainMachine hourly. Although is possible to send data more often the
RainMachine Mixer (that aggregates data from multiple sources) runs only hourly.

### Download

https://github.com/sprinkler/rainmachine-weewx/archive/weewx-rainmachine-v0.2.tar.gz

### How to Install

1.  Run the extension installer:

```
wee_extension --install weewx-rainmachine-v0.2.tar.gz
```

2.  Modify weewx.conf:

```
[StdRESTful]
    [[RainMachine]]
        token = RAINMACHINE_ACCESS_TOKEN
        ip = RAINMACHINE_IP_ADDRESS

```

3.  Restart weewx

```
sudo /etc/init.d/weewx stop
sudo /etc/init.d/weewx start
```
