WeeWX V4.x offers a new schema `wview_extended`, with many new types. You may
find many of these types useful and want to switch to it. Before explaining how
to do this, it's worth noting:

1. In WeeWX, the schemas found in `schemas/wview.py` and
   `schemas/wview_extended.py` are used _only when the database is created_.
   Thereafter, the schema is read from the database itself, and the Python
   versions are not used again.

2. While `wview_extended` is the new, default, schema for V4.x, it is not
   necessary to "upgrade" to it. Your old `wview` schema will continue to work just
   fine. Switch to the new schema only if you find the new types useful.

3. If you just need to add one or two new types, you may find it easier to
   use the tool [`weectl database
   add-column`](https://www.weewx.com/docs/5.0/utilities/weectl-database/#add-a-new-observation-type-to-the-database).

Here's how to switch. Note that these instructions are just a variant on the
instructions in section [_Reconfigure database using a new
schema](https://www.weewx.com/docs/5.0/custom/database/#reconfigure-using-new-schema)
of the _Customizing Guide_.

**These instructions assume that you are using WeeWX V5.x**

1. We will be using the utility
   [`weectl database`](https://www.weewx.com/docs/5.0/utilities/weectl-database/)
   to do the switch.

2. Stop `weewxd`.

3. **Modify `wx_binding`**. When it creates the new, modified database,
   `weectl database` needs to know to use the `wview_extended` schema instead of
   the old, `wview` schema. You do this by changing the option `schema` in
   section `[DataBindings]` in `weewx.conf`.

   The `[DataBindings]` section now looks like this:

   ```ini
   [DataBindings]
      [[wx_binding]]
          # The database option is unchanged. If you are using MySQL, it should be archive_mysql
          database = archive_sqlite
          table_name = archive
          # Older installs will use weewx.wxmanager.WXDaySummaryManager instead of weewx.manager.DaySummaryManager. Doesn't matter.
          manager = weewx.manager.DaySummaryManager
          # Specify the new, extended schema here:
          schema = schemas.wview_extended.schema
   ```

   Note that for the `manager` option, you can use either `weewx.manager.DaySummaryManager` or `weewx.wxmanager.WXDaySummaryManager`.

4. **Check permissions**. `weectl database` will create a new database with the same name as the old, except with the suffix `_new` attached to the end. Make sure you have the necessary permissions to create it. In particular, if you are using MySQL, you will need `CREATE` privileges:

   ```SQL
   mysql> GRANT select, update, create, delete, insert ON weewx_new.* TO weewx@localhost;
   ```

5. **Create and populate the new database**. Now run the utility `weectl
   database` with the `reconfigure` action. If your configuration file
   `weewx.conf` is in an unusual position, you may have to specify its path
   using the `--config` option.

   ```shell
   weectl database reconfigure
   ```

   This will create a new database (nominally, `weewx.sdb_new` if you are using SQLite, `weewx_new` if you are using MySQL) using the new schema and populate it with data from the old database.

6. **Shuffle the databases**. 

   **Warning!
   Make a backup of the data before doing any of the next steps!**

   For this step, we will shuffle the databases around so the new database has the name of the old database.

   For SQLite:

   ```shell
   cd <sqlite directory>
   mv weewx.sdb_new weewx.sdb
   ```

   where `<sqlite directory>` represents the location of your database. If you did a package (e.g., Debian) install, this would generally be `/var/lib/weewx`. If you did a `setup.py` install, it's `/home/weewx/archive`.

   For MySQL:

   ```sql
   mysql -u <username> --password=<mypassword>
   mysql> DROP DATABASE weewx;                             # Delete the old database
   mysql> CREATE DATABASE weewx;                           # Create a new one with the same name
   mysql> RENAME TABLE weewx_new.archive TO weewx.archive; # Rename to the nominal name
   ```

7. **Build the daily summaries**. Finally, the daily summaries inside the database have to be built. This will be done automatically by `weewxd` when it starts up, but it's good practice to do this manually, as this process can take some time:

   ```shell
   weectl database rebuild-daily
   ```
8. Restart `weewxd`.


