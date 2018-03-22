WeeWX recognizes three different kinds of pressure.

* Station or Gauge Pressure (key `pressure`): This is the absolute, raw pressure as measured by your instrument. It is not corrected for altitude or pressure. Pilots call this QFE.
* Altimeter Pressure (`altimeter`) : This is the pressure corrected for altitude, using a standard temperature profile. Pilots call this QNH.
* Sea-level Pressure (`barometer`): This is the pressure corrected for altitude, temperature, and (frequently) humidity. Pilots call this QFF. This is the value displayed by the standard skin.

Some weather stations emit only one, others two, and a few all three types of pressure. Regardless, the weeWX service `StdWXCalculate` tries to calculate any missing values from the information it has at hand. Because some of them require extra information, it may not always be possible to do a calculation. For example, `barometer` requires the temperature 12 hours ago in order to correct for diurnal tide effects. This may not be available. If `StdWXCalculate` cannot calculate a value, it sets it to Python `None`.

The various upload sites require different values.

| Site         | Value       |
|--------------|-------------|
| Wunderground | `barometer` |
| CWOP         | `altimeter` |
| AWEKAS       | `barometer` |

Be careful when comparing pressures from weeWX with the pressure(s) reported by the weather station consoles.  Some weather station consoles display only the sea-level pressure (`barometer`), while others can display the station pressure (`pressure`).  For example, the FineOffset consoles can display either the station pressure (called 'absolute' pressure) or the station pressure plus an offset (called 'relative' pressure).  The Acurite consoles display an approximation of sea-level pressure whose value fluctuates depending on how long the console has been running.
