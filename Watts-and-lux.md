# Meta

Authored by user Greg Troxel, from an [email exchange](https://groups.google.com/d/msg/weewx-user/vPkAixmKba8/-HJnGPxHGgAJ).  Edited over time, likely also by others

# Irradiance and Illuminance are not the same thing

Sensors like the Davis solar radiation sensor measure "irradiance" in W/m&sup2;, which is the amount of power arriving in an area.  By definition, power at all wavelengths is treated equally.

Some stations meausure "illuminance" in lux, which is a measure of the amount of light in an area in terms of the response of the human eye, according to a standardized response curve obtained from experiments.

Typically people care about illuminance for management of artificial
lighting or light pollution, and the values are very low compared to
values that can be read on a solar radiation sensor.

# Obtaining illuminance

For a single wavelength, one can ask what the value of one measurement
is given the other, multiplying or dividing by the standardized response
curve.  Using a spectroradiometer, one can measure the power at a
variety of wavelengths, say at 10 nm intervals, and then sum the
converted powers.

Another way to obtain lux is to use a device with a filter that matches
the standard curve.   There are instruments for lighting, and for
photography that do this.   There are also sky brightness meters (which
I have read about but do have not experience with):
[http://unihedron.com/projects/darksky/](http://unihedron.com/projects/darksky/).

# Attempting to convert

t simply does not make technical sense to convert a sensor value in W/m&sup2;
to lux (which is lm/m&sup2;, lm being lumen).  They are incompatible
units measuring different things.

This [stackexchange answer](https://physics.stackexchange.com/questions/89181/how-is-the-earth-heated-by-a-full-moon#89197) claims 6.8 mW/m&sup2; at full moon, and a full
moon is known to produce about 0.3 lux.  Note that this ratio cannot be
applied to sunlight; moonlight has a very high color temperature,
meaning that it has more blue than red compared to sunlight.

So basically, you just cannot convert sensor output and weewx should not
try.  If you want to measure lux you need a lux sensor, and to measure
W/m&sup2; you need a radiation sensor.

One could go out on a limb and start making assumptions about the
spectral distribution of different kinds of daylight that are likely for
various irradiance levels.  For example, full sun with no clouds is well
characterized.  But as soon as you get into dawn/dusk and clouds, there
is much more variability.  Trying to guess spectral distribution based
on illuminance or irradiance and converting, and comparing that to a
sensor that measures the other, would be an interesting science
experiment.

# References

  - [https://en.wikipedia.org/wiki/Photometry_(optics)](https://en.wikipedia.org/wiki/Photometry_(optics))
  - https://help.ambientweather.net/help/why-is-the-lux-to-w-m-2-conversion-factor-126-7/
  - https://cumulus.hosiene.co.uk/viewtopic.php?t=3979
  - [A conversion guide: solar irradiance and lux illuminance (journal article)](https://www.extrica.com/article/21667/pdf)