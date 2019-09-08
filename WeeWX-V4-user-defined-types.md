# Overview

Right now, the set of observation types is fixed.

- The set of types that `StdWXCalculate` knows how to calculate is fixed. This proposal is to make it easy to add new, user-defined types.
- Heating and cooling degree days are also special types, defined in subclass `WXDaySummaryManager`.
- Some types require highly dynamical calculations, such as calculating power from total energy consumption. 

In theory, new types can be introduced by subclassing, but this allows only new types to be accreted in a linear fashion: it would not be possible for two extensions to both introduce new types. One would have to inherit from the other.

# Goals
- There would be no special, magic types. Instead, new types could be added dynamically. For backwards compatibility,  The "well-known" types calculated by `StdWXCalculate`, such as `dewpoint`, or `heatindex` would be included out of the box.
- Allow new user-defined aggregates to be calculated. 
- Allow new user-defined time series to be calculated. 

# Defining new types
Adding a new observation type is done by writing a class with the following interface:

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

Note that if an observation type is unknown to the class, it should raise an exception of type `weewx.UnknownType`, *not* return a value of `None`. A value of `None` is reserved for a known type, but one that the class is unable to calculate, perhaps because an input values is missing.

# Aggregation
In a similar manner, a new aggregation is added using a class with the following interface:

```python
class MyVector(object):

    def get_aggregate(self, obs_type, timespan,
                      aggregate_type=None,
                      aggregate_interval=None):

        if obs_type.starts_with('ch'):
            "something"

        else:
            raise weewx.UnknownType(obs_type)
```
