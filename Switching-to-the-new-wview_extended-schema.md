WeeWX V4.x offers a new schema `wview_extended`, with many new types. You may find many of these types useful and want to switch to it. Before explaining how to do this, it's worth noting:

1. In WeeWX, the schemas found in `schemas/wview.py` and `schemas/wview_extended.py` are used _only when the database is created_. Thereafter, the schema is read from the database itself, and the Python versions are not used again.
2. While `wview_extended` is the new, default, schema for V4.x, it is not necessary to "upgrade" to it. Your old `wview` schema will continue to work just fine with V4.x. Switch to the new schema only if you find the new types useful.
3. If you are using MySQL, and if you need just one or two of the new types, you may find it easier to simply augment the table by using the SQL command [AUGMENT TABLE](https://www.mysqltutorial.org/mysql-add-column/). This allows you to change the database schema _in situ_, and is much faster. Unfortunately, SQLite does not have an analogous command.

Here's how to switch. Note that these instructions are just a variant on the instructions in section [_Adding a new type to the database_](http://www.weewx.com/docs/customizing.htm#add_archive_type) that can be found in the Customizing Guide.

**These instructions assume you are using WeeWX V4.x**

1. We will be using the utility [`wee_database`](http://www.weewx.com/docs/utilities.htm#wee_database_utility) to do the switch.
2. Stop weewxd.
3. **Modify `wx_binding`**. When it creates the new, modified database, `wee_database` needs to know to use the `wview_extended` schema instead of the old, `wview` schema. You do this by changing the option `schema` in section `[DataBindings]` in `weewx.conf`.

   The `[DataBindings]` section now looks like this:

   ```ini
   [DataBindings]
      [[wx_binding]]
          # The database option is unchanged. If you are using MySQL, it should be archive_mysql
          database = archive_sqlite
          table_name = archive
          # Older installs will use WXDaySummaryManager instead of DaySummaryManager. Doesn't matter.
          manager = weewx.manager.DaySummaryManager
          # Specify the new, extended schema here:
          schema = schemas.wview_extended.schema
   ```

   Note that for the `manager` option, you can use either `weewx.manager.DaySummaryManager` or `weewx.manager.WXDaySummaryManager`.

4. **Check permissions**. `wee_database` will create a new database with the same name as the old, except with the suffix `_new` attached to the end. Make sure you have the necessary permissions to create it. In particular, if you are using MySQL, you will need `CREATE` privileges:

   ```SQL
   mysql> GRANT select, update, create, delete, insert ON weewx_new.* TO weewx@localhost;
   ```
5. **Create and populate the new database**. Now run the utility `wee_database` with the `--reconfigure` option. If your configuration file `weewx.conf` is in an unusual position, you may have to give its path as an argument.

   ```shell
   wee_database weewx.conf --reconfigure
   ```

   This will create a new database (nominally, `weewx.sdb_new` if you are using SQLite, `weewx_new` if you are using MySQL) using the new schema and populate it with data from the old database.

6. **Shuffle the databases**. Now arrange things so WeeWX can find the new database.

   **Warning!
   Make a backup of the data before doing any of the next steps!**

   You can either shuffle the databases around so the new database has the same name as the old database, or edit `weewx.conf` to use the new database name. To do the former:

   For SQLite:

   ```shell
   cd SQLITE_ROOT
   mv weewx.sdb_new weewx.sdb
   ```

   For MySQL:

   ```sql
   mysql -u <username> --password=<mypassword>
   mysql> DROP DATABASE weewx;                             # Delete the old database
   mysql> CREATE DATABASE weewx;                           # Create a new one with the same name
   mysql> RENAME TABLE weewx_new.archive TO weewx.archive; # Rename to the nominal name
   ```
7. Restart weewxd

It's worth noting that there's actually a hidden, last step: rebuilding the daily summaries inside the new database. This will be done automatically by WeeWX when it starts up. Alternatively, it can be done manually using the `wee_database` utility and the `--rebuild-daily` option:

```shell
wee_database weewx.conf --rebuild-daily
```

