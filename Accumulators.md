# Accumulators

## Abstract

This Wiki documents the accumulators. Accumulators ingest an irregular stream of LOOP packets,
then, when requested, allows regular archive records to be extracted from them. Most of the time,
they "do the right thing," but there may be occasions where customization is called for.

An accumulator is instantiated for each observation type. Which type is instantiated is set
by option `accumulator` (described later). There are three choices

- Scalars: accumulate scalar quantities, such as temperature;
- Vectors: accumulate vector quantities, such as wind; and
- FirstLast: primarily used to accumulate strings.

The vast majority of the time, you will be working with scalars and, indeed, this is the
default type of accumulator.

Whatever their type, all accumulators are expected to perform three different functions:
- How to add new values of a type (the 'adder');
- How to merge a type with another accumulator (the 'merger'); and
- How to extract a type out of its accumulator, and into a record (the 'extractor').

Again, there are sensible defaults for these three roles, but this can be customized.

## Life cycle of an accumulator

Class `weewx.accum.Accum` is a *collection* of accumulators. At the start of an archive interval, a
new instance is created. Initially, it is empty.

As LOOP packets come in, each observation type is checked to see if it's already in the instance of
`Accum`. If not, a new accumulator of the proper type is created for it. The observation type is
then added to its accumulator using the appropriate "adder".

At the end of the archive period, it is time to convert the accumulators into an archive record.
This is done for each type, using the appropriate "extractor".

This archive record must then be merged into the already existing daily summaries. This is done
for each type, using the appropriate "merger."

Finally, the collection of accumulators is discarded, and the cycle begins again.
 
## Configuration
For each observation type, a type of accumulator, an adder, a merger, and an extractor can be
specified, all through the `weewx.conf` file. The format is:

```ini
[Accumulator]
    ...
    [[a_type]]
        accumulator = [scalar | vector | firstlast]
        adder = [add | add_wind | check_units | noop]
        merger = [minmax | avg]
        extractor = [avg | sum | min | max | count | first | last | wind | noop]
```

These are defined in more detail below.

### Accumulators
Presently, there are three different kinds of accumulators

- `scalar`. This is the "normal" kind. It accumulates quantities such as sum, min, max, count,
etc., suitable for a simple value, either integer or floating point. It is the default accumulator.
- `vector`. This is similar to scalar, except it accumulates a vector field, such as wind,
consisting of two parts. a magnitude and a direction. It accumulates the sum of magnitude as well,
as x- and y-components of the wind. 
- `firstlast`. This accumulator accumulates only the first and last entry it has seen, along with
their times. It is useful for things like strings, although there is no reason it can't be used for
other things.
 
### Adder
As new LOOP packets come in, each type must be incorporated into the accumulators. The job of the
adder is to do this incorporation.  There are four different kinds of adders:

- `add`. This one simply adds to the running total, and updates min and max. In the case of the
`firstlast` accumulator, it updates the first and last values seen. It is the default.
- `add_wind`. This one adds a wind value.
- `check_units`. This one simply checks to make sure the incoming unit system is the same as the
already seen unit system. It is typically only used by type `usUnits`.
- `noop`. Do nothing. Don't add to the accumulator at all.

### Merger
At the end of an archive interval, one of the tasks that must be performed is updating the
database's daily summaries. This means merging the finished accumulator with the running total held
in the database. It is the job of the merger to manage this merge. There are two kinds of mergers:

- `minmax`. This is what you'll want most of the time. It is the default.
- `avg`. This merger is used if the user has set option [`loop_hilo`](http://www.weewx.com/docs/usersguide.htm#StdArchive) to `False`.

### Extractor

At the end of an archive interval, it is time to extract an archive record out of the accumulators.
The job of the extractor is to do this for each type. There are eight different kinds of
extractors:

- `avg`: Extract the average value of this type. This is the default.
- `count`: Extract the total number of non-Null LOOP packets seen of this type.
- `first`: Extract the first value seen of this type (introduced WeeWX V5).
- `last`: Extract the last value seen of this type. Use this type for totals of extensive 
  variables, such as `dayRain`.
- `max`: Extract the maximum value seen of this type.
- `min`: Extract the minimum value seen of this type.
- `noop`: Extract no value for this type.
- `sum`: Extract the total seen for this type. Use this type for `rain` or other  variables that 
  are measured increments.
- `wind`: Extract `windSpeed` as the average wind magnitude, `windDir` as the vector average
  direction, `windGust` as the maximum wind seen, and `windGustDir` as its direction.

## Defaults
The accumulators come with a set of defaults that covers most situations.

```ini
[Accumulator]
    [[dateTime]]
        adder = noop
    [[dayET]]
        extractor = last
    [[dayRain]]
        extractor = last
    [[ET]]
        extractor = sum
    [[hourRain]]
        extractor = last
    [[rain]]
        extractor = sum
    [[rain24]]
        extractor = last
    [[monthET]]
        extractor = last
    [[monthRain]]
        extractor = last
    [[stormRain]]
        extractor = last
    [[totalRain]]
        extractor = last
    [[usUnits]]
        adder = check_units
    [[wind]]
        accumulator = vector
        extractor = wind
    [[windDir]]
        extractor = noop
    [[windGust]]
        extractor = noop
    [[windGustDir]]
        extractor = noop
    [[windGust10]]
        extractor = last
    [[windGustDir10]]
        extractor = last
    [[windrun]]
        extractor = sum
    [[windSpeed]]
        adder = add_wind
        merger = avg
        extractor = noop
    [[windSpeed2]]
        extractor = last
    [[windSpeed10]]
        extractor = last
    [[yearET]]
        extractor = last
    [[yearRain]]
        extractor = last
    [[lightning_strike_count]]
        extractor = sum
```  

These can be overridden by adding a section `[Accumulator]` to the configuration file `weewx.conf`.
Here's an example. By default, the average value of `outTemp` of all the LOOP packets in an archive
interval is used for the value of `outTemp` in an archive record. Suppose you want the very last
value instead. This is what you would put in `weewx.conf`:

```ini
[Accumulator]
    [[outTemp]]
        extractor = last
``` 