# Migrating `setup.py` installs to Version 5.0

This is a guide for migrating V4.x installations that were installed using the
`setup.py` method, to Version 5.0, using pip.

## Code vs station data

With V5.0, there is now a clean separation between *WeeWX code*, and *station
data*. They are stored in separate areas, rather than everything under
`/home/weewx`.

### WeeWX code

While there are [many ways to install WeeWX using pip](pip-install-strategies),
if you follow the WeeWX install recommendations, pip will install your code in a
_virtual environment_, located in `~/weewx-venv` in your home directory.

This contrasts with the `setup.py` method, which installed everything under
`/home/weewx/bin`.

### Station data

By contrast, *station data* is specific to your station. By default, it is now
stored in your home directory in `~/weewx-data`, although you may continue to
use your old station data located in `/home/weewx` by following this guide.
Station data includes:

* The configuration file, `weewx.conf`;
* Skins;
* Database;
* Generated HTML files and images; and
* Extensions (Python code specific to a station).

With this in mind, here is how to install Version 5, while continuing to use the
old station data in `/home/weewx`.

## Migration process

1. Install V5.0 by using the tool `pip`. See the guide [_Installation using
   pip_](https://weewx.com/docs/5.0/quickstarts/pip/), but note that you are _only
   going to follow the first step_ [_Install in a virtual
   environment_](https://weewx.com/docs/5.0/quickstarts/pip/#install-in-a-virtual-environment).
   Do not do step 2, _Provision a new station_.
 
    When you are done, the new V5.0 executable will be in `~/weewx-venv/bin/weewxd`,
    rather than the more familiar V4.x location `/home/weewx/bin/weewxd`.

    For what follows, make sure your virtual environment is still active. When in
    doubt, you can always reactivate it:

        source ~/weewx-venv/bin/activate

2. You do not want to do Step 2, _Provision a new station_, because you will be 
   simply reusing your old station data in `/home/weewx`. However, you do need to 
   upgrade your old examples and daemon utility files to the V5 versions.

        weectl station upgrade --config=/home/weewx/weewx.conf

3. At this point, try running the V5.0 version of `weewxd` directly, using your
   old configuration file.

        weewxd --config=/home/weewx/weewx.conf

4. If that works, then it's time to replace your old daemon configuration file
   with one that uses the new V5.0 executable. These steps will require root 
   privileges.

    ```shell
    # systems with systemd
    # This will run weewx as you, not root.  If you prefer to run as
    # the root user, modify the weewx.service file.
    sudo cp /home/weewx/util/systemd/weewx.service /etc/systemd/system
    sudo systemctl daemon-reload
    sudo systemctl enable weewx
    sudo systemctl start weewx
    ```

    ```shell
    # systems with sysVinit
    sudo cp /home/weewx/util/init.d/weewx.debian /etc/init.d/weewx
    sudo chmod +x /etc/init.d/weewx
    sudo update-rc.d weewx defaults 98
    sudo /etc/init.d/weewx start     
    ```
 
    ```shell
    # macOS
    sudo cp /Users/Shared/weewxutil/launchd/com.weewx.weewxd.plist /Library/LaunchDaemons
    sudo launchctl load /Library/LaunchDaemons/com.weewx.weewxd.plist
    ```

## Move your old code aside

Note that your old V4.x code will still be under `/home/weewx/bin` 
(`/Users/Shared/weewx` for macOS).

To avoid confusing yourself and any tools that you might use, you should
consider moving your old code aside. Unfortunately, you cannot simply rename it,
because your `user` directory is located underneath, so you would lose access to
it and any extensions it might contain. Here's how to do it without disturbing
the things you want to keep:

### Linux
```shell
cd /home/weewx
mv bin bin.old
mkdir bin
cp -r ./bin.old/user bin 
```

### macOS
```shell
cd /Users/Shared/weewx
mv bin bin.old
mkdir bin
cp -r ./bin.old/user bin 
```

When you're done, you should have a directory tree that looks something like this:

```
bin
└── user
    ├── __init__.py
    ├── extensions.py
    └── installer
bin.old
    ├── daemon.py
    ├── schemas
    ... etc
```
