# How to calculate values in templates


If you need to calculate a new value, you should generally strive to do it in a search list
extension or XType. However, there are occasions where it is quicker and easier to do
the calculation right in the template, using the Cheetah directive <span class="code">#set</span>.

Here's an example where we calculate the elapsed time between dawn and dusk:

```
#set $dawntodusk = $almanac(horizon=-6).sun(use_center=1).set.raw - $almanac(horizon=-6).sun(use_center=1).rise.raw
```

The expression calculates the simple difference between two values: the time of the sunset minus the time of
the sunrise, both in unix epoch time. The result will be the elapsed time in seconds. For details on the
`$almanac` tag, see [Almanac](https://weewx.com/docs/customizing.htm#Almanac). For details on the
`.raw` suffix, see [Formatting options](https://weewx.com/docs/customizing.htm#formatting_options).

We can make use of this tag by simply referencing `$dawntodusk`. For example:

```
<p>The elapsed time between dawn and dusk is $dawntodusk</p>
```

This will result in something like

```
The elapsed time between dawn to dusk is 57315.0023124218
```

Not very elegant.

### Improving readability

To get something more readable, while having the results honor any user-specified
settings, including unit conversions and formatting, is a bit more involved.
Here's a more sophisticated version that leverages classes [ValueTuple](https://weewx.com/docs/customizing.htm#ValueTuple) 
and [ValueHelper](https://weewx.com/docs/customizing.htm#ValueHelper) to do the formatting.
While this example does not involve any unit conversions, in general, these classes can take care of
that as well.

```
#from weewx.units import ValueTuple, ValueHelper                                                                   ## 1
#set $dawn = $almanac(horizon=-6).sun(use_center=1).rise.raw                                                       ## 2
#set $dusk = $almanac(horizon=-6).sun(use_center=1).set.raw                                                        ## 3
#set $dawntodusk = $dusk - $dawn                                                                                   ## 4
#set $dawntodusk_vh = ValueHelper(ValueTuple($dawntodusk,'second','group_deltatime'),formatter=$station.formatter) ## 5
```

The numbers reference the comments below:

1. Neither class `ValueTuple`, nor `ValueHelper`, appear in the template. We must import them.

2. Set the value `$dawn` to the time of sunrise. The `.raw`
suffix ensures that the results will be in unix epoch time (rather than a string).
 
3. Similar, but with sunset.
 
4. Take the difference. Because unix epoch time is in seconds, the results will also be in seconds.

5. Construct a `ValueHelper` to hold the results. We borrow a formatter from the `$station` tag.


The results, `$dawntodusk_vh`, is a `ValueHelper`. When
rendered as a string by the Cheetah engine, it will do the necessary magic to ensure sensible formatting
for the `ValueTuple` that it holds internally. This time, the template

```
<p>The elapsed time between dawn and dusk is $dawntodusk_vh.</p>
```

gives the following results:

```
The elapsed time between dawn and dusk is 0 days, 15 hours, 55 minutes
```

Like any other instance of `ValueHelper`, you can override the formatting.
For example, specifying

```
The elapsed time between dawn and dusk is
    $dawntodusk_vh.format("%(hour)d%(hour_label)s, %(minute)d%(minute_label)s, and %(second)d%(second_label)s")
```    

gives the results

```
The elapsed time between dawn and dusk is 15 hours, 55 minutes, and 15 seconds
```
