Many types of hardware report wind speed, and many report wind gust.  But wind gust on one weather station may not be the same thing as wind gust from a station built by a different vendor.

## What is wind gust?

First of all, there are a few different terms for quantifying "how windy is it?"

### Wind speed

You might think that wind speed is pretty easy - its just a measure of how fast the wind is blowing, right?

The US National Weather Service defines wind speed in terms of a sustained speed over a period of time.  For example `wind speed` is defined as:

> The rate at which air is moving horizontally past a given point. It may be a 2-minute average speed (reported as wind speed) or an instantaneous speed (reported as a peak wind speed, wind gust, or squall).

As another example, there is also a `20-foot wind`, defined as:

> Sustained winds averaged over a 10 minute period and measured 20 feet above the average height of nearby vegetation. This is the standard reported by the Remote Automated Weather Stations (RAWS) owned by land management agencies and used in the National Fire Danger Rating System (NFDRS).

When it comes to actually measuring the wind speed, it depends on the type of hardware.  For example, a PEET Ultimeter reports wind speed as fast as twice per second, a Davis Vantage reports wind speed every 2.3 seconds, an Acurite 5-in-1 reports wind speed every 18 seconds, and most Fine Offset stations report wind speed every 48 to 60 seconds.  Any measure of speed is an approximation - it is an average over a sample period.  But stations that sample more frequently should provide a closer approximation.

Contrast this with the anemometer on a sailing vessel.  These devices come very close to reporting real-time, instantaneous wind speeds.

So when an Ultimeter reports `windSpeed`, it is pretty close to instantaneous wind speed.  But the `windSpeed` reported by an Acurite station is actually an average wind speed.

None of them report the 'sustained winds' definition.

### Wind gust

The textbook definition of a wind gust is:

> A sudden, brief increase in the speed of the wind, followed by a lull.

The US National Weather Service defines a wind gust as

> Rapid fluctuations in the wind speed with a variation of 10 knots or more between peaks and lulls. The speed of the gust will be the maximum instantaneous wind speed.

or:

> When the peak wind speed reaches at least 16 knots and the variation in wind speed between the peaks and lulls is at least 9 knots. The duration of a gust is usually less than 20 seconds.

Many types of weather station hardware report only wind speed.  For example, the PEET Ultimeter, Acurite 5-in-1, and LaCrosse WS23xx stations do not report wind gust.  Those that do report wind gust have different algorithms for calculating it.  Most are some measure of a maximum speed over a sampling period.  But since their methods of sampling speed differ, the resulting gust will differ from one type of station to another.

The wind 'gust' reported by these stations is 'maximum wind speed' over some time period, not, in fact, 'wind gust'.

### Wind direction

Most hardware will sample the wind direction when it samples the wind speed.  However, there are many different approaches.  Some hardware associates a wind direction for each wind speed sample.  Other hardware reports a single wind direction sample for an average wind speed.  There are even more permutations when wind gust is reported.  Some stations report a wind gust, but no wind gust direction.  Others report a direction for each wind gust.

## Sensor placement

The placement of the anemometer matters, as does the design of the instrument itself.  If the anemometer is in the wind shadow of a tree or roof line, it might experience vortex shedding or other effects that show up as frequent gusts.  The impeller design of LaCrosse WS23xx stations was replaced by a cup design so it would react better to wind shifts.

## So where does that leave us?

Accuracy is one of the primary goals of weeWX.  This means that, whenever possible, a driver in weeWX will report only what the hardware reports.  So if a weather station reports wind gust, but not wind gust direction, the driver will not fabricate data just to fill in a direction.

WeeWX does not enforce National Weather Service definitions for wind speed or wind gust.  If the hardware reports wind gust, weeWX will use those values, whether they are actual NWS gust or simply maximum wind speed.  If no wind gust is reported by the hardware, the accumulators in weeWX will use the highest reported wind speed over an interval as the wind gust.

## References

http://w1.weather.gov/glossary/

https://graphical.weather.gov/definitions/
