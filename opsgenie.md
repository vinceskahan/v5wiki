### OpsGenie Service

The OpsGenie Service is an extension that enables the sending of Heartbeats and Alerts to the cloud alert service [OpsGenie](https://www.opsgenie.com). OpsGenie has a __free__ version that allows plenty functionality to use standard Heartbeats and Alerts. If you need features like teams and escalations there are paid levels available. See [OpsGenie Pricing](https://www.opsgenie.com/pricing) - click on _See All Details_ to see what's included in the free version.

#### Heartbeats
You can send a regular Heartbeat to OpsGenie to let it know your Weewx process is alive and has not crashed, or that your server has not crashed, or your internet has gone down and your server may not be sending updates online. The default Heartbeat interval is 5 minutes. Use this with an OpsGenie Heartbeat timeout set to 10 minutes. See [OpsGenie Heartbeats](https://www.opsgenie.com/docs/miscellaneous/heartbeat-monitoring) _Note: Heartbeats+ is a paid feature but is not required for the basic Heartbeat functionality_

#### Alerts
You can send an Alert based on any valid Python expression using Weewx LOOP packets. An example is that you can send an Alert based on when it starts raining so you can grab the washing in. Alerts can be sent via email and smartphone app (Free) or by SMS or Voice (Paid). [OpsGenie Alerts](https://www.opsgenie.com/docs/getting-started/overview)

### Download
<https://github.com/dcapslock/Weewx-OpsGenie/releases/download/v0.1.1/Weewx-OpsGenie.tar.gz>

### How to install

1. Run the extension installer

   ```
wee_extension --install weewx-opsgenie.tar.gz
```

2. Modify weewx.conf. Your OpsGenie API Key can be found on the OpsGenie Integrations page. See

   ```
    [OpsGenie]
        [[Heartbeat]]
            apiKey = YOUR-HEARTBEAT-API-KEY #Set to OpsGenie Heartbeat Integration API Key
            heartbeatName = YOUR-HEARTBEAT-NAME #Set to the name of the heartbeat in OpsGenie
            send_heartbeat = True
        [[Alerts]]
            Source = YOUR-SOURCE #Can also be set at the Alert level
            apiKey = YOUR-ALERT-API-KEY #Set OpsGenie Alert apiKey and modify and add alerts below
            ExpressionUnits = YOUR-DESIRED-UNITS #Set to units you wish to evaluate expressions in: US, METRIC, METRICWX
            Entity = YOUR-ENTITY #Can also be set at the Alert level
            [[[IsRaining]]]
                Description = It is raining!
                Recipients = YOUR-OPSGENIE-RECIPIENT-LIST (USER EMAIL ADDRESS OF USER)
                Tags = WARN
                Alias = IsRaining
                Details = preceipType, rainRate
                Message = It is raining!
                Expression = precipType > 0
```

3. Restart Weewx

   ```
sudo /etc/init.d/weewx start
```

See [readme.txt](https://raw.githubusercontent.com/dcapslock/Weewx-OpsGenie/master/Weewx-OpsGenie/readme.txt) for more information on config options.



__Current Version v0.1.1__