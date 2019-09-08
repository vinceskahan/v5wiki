# Overview

Right now, the set of observation types is fixed. In theory, it can be extended, but only through subclassing. This proposal is to make it easy to add new, user-defined types.

# Goals
- Allow the service `StdWXCalculate` to calculate and add new, user-defined types.
- Allow new user-defined aggregates to be calculated. 
- Allow new user-defined time series to be calculated. 

# Defining new types
A new type is introduced by implementing a class with the following interface

```python
import weewx
import weewx.wxformulas

class MyTypes(object):
   def get_value(self, obs_type, record, db_manager):
       if obs_type == 'dewpoint':
           if record['usUnits'] == weewx.US:
               return weewx.wxformulas.dewpointF(record.get('outTemp'), record.get('outHumidity'))
           elif record['usUnits'] == weewx.METRIC or record['usUnits'] == weewx.METRICWX:
               return weewx.wxformulas.dewpointC(record.get('outTemp'), record.get('outHumidity'))
           else:
               raise ValueError("Unknown unit system %s" % record['usUnits'])
       else:
           raise weewx.UnknownType(obs_type)
```
            

