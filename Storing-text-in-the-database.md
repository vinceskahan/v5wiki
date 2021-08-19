Most of the time, you want to store floating point numbers and integers in the database, but there may be occasions where
you want to store text. This is how to do it for SQLite databases.

1. Assuming you're using SQLite, make sure you read their documentation on data types: https://sqlite.org/datatype3.html

2. Declare the observation in the schema, specifying `TEXT`. Here is a very simple example, involving only barometric pressure, outside temperature, and a text comment. Do not include a daily summary for the text type: 

    ```python
    table = [('dateTime',  'INTEGER NOT NULL UNIQUE PRIMARY KEY'),
             ('usUnits',   'INTEGER NOT NULL'),
             ('interval',  'INTEGER NOT NULL'),
             ('barometer', 'REAL'),
             ('outTemp',   'REAL'),
             ('comment',   'TEXT')
             ]

    day_summaries = [('barometer', 'scalar'), ('outTemp', 'scalar')]

    schema = {
        'table': table,
        'day_summaries' : day_summaries
    }
    ```
    Note how daily summaries will be created for `barometer` and `outTemp`, but not `comment`.

3. See the directions in the Customizing Guide for how to specify that your new schema be used. [Picking a starting schema](https://www.weewx.com/docs/customizing.htm#Picking_a_starting_schema).

4. Read the developer's notes on [Accumulators](https://github.com/weewx/weewx/wiki/Accumulators). In your `weewx.conf` file, specify that your text type should be accumulated using the `firstlast` accumulator. This will prevent it from trying to find the average, etc, of a string. You will also need to specify an extractor, which determines how a record will be extracted from the accumulator. You can choose either `first` (the first record seen during the archive period), or `last` (the last record):
    ```ini
    [Accumulator]
        [[comment]]
            accumulator = firstlast
            extractor = last
    ```

That's it. Of course, you will have to make your own arrangements for the type `comment` to appear in either LOOP packets or archive records.

You can use your new type as tags. Make sure you specify the `.raw` suffix, so that the Cheetah generator doesn't try to format your string as a number.

```html
    <p>Current comment = $current.comment.raw</p>
    <p>First comment of the day = $day.comment.first.raw</p>
    <p>Last comment= $day.comment.last.raw</p>
```