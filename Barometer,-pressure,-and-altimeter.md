Weewx recognizes three different kinds of pressure.

* Station or Gauge Pressure (key `pressure`): This is the pressure as measured by your instrument. Pilots call this QFE.
* Altimeter Pressure (`altimeter`) : This is the pressure corrected for altitude, using a standard temperature profile. Pilots call this QNH.
* Sea-level Pressure (`barometer`): This is the pressure corrected for altitude, using a profile from the measured temperature and (frequently) humidity. Pilots: QFF. This is the value displayed by the standard skin.

Some weather stations emit only one, others two, and a few all three. Regardless, the weewx service `StdWXCalculate` tries to calculate any missing values from the information it has at hand. Because some of them require extra information, it may not always be possible to do a calculation. For example, `barometer` requires the temperature 12 hours ago in order to correct for diurnal tide effects. This may not be available. If `StdWXCalculate` cannot calculate a value, it sets it to Python `None`.

The various upload sites require different values.

| Site         | Value       |
|--------------|-------------|
| Wunderground | `barometer` |
| CWOP         | `altimeter` |
| AWEKAS       | `barometer` |



