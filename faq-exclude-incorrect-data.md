## How to exclude obviously incorrect data

Many stations have sensors that emit data that is obviously incorrect.

You may:
 * choose to [calibrate](http://www.weewx.com/docs/usersguide.htm#StdCalibrate) your sensor to apply a correction to what it reports
 * alternately [apply min/max limits](http://www.weewx.com/docs/usersguide.htm#StdQC) to exclude data outside reasonable bounds

Please read the above links from the User's Guide carefully, as there is some ordering in how WeeWX processes these stanzas.

### Some more complicated examples

One recently posted example shows how flexible WeeWX can be.  In this case the station can emit a lightning distance reading yet show a lightning strike count of zero.  The example below ensures that lightning distance is not recorded unless there is a corresponding strike count during that interval.

```
[StdCalibrate]    
    [[Corrections]]
        lightning_distance = lightning_distance if Atlas_stike_count > 0 else None
```
