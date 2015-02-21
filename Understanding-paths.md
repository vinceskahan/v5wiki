## Working with paths in a Unix environment

Understanding how paths work is a rather important part of working with the command-line on any Linux/BSD/Unix system.  The `PATH` environment variable tells the system where to look for commands.  It is a colon-delimited list of paths.  In the case of weewx, there are typically two paths that matter most: the path to the command and the path to the `weewx.conf` configuration file.

Let's start with a basic weewx operation: running weewx directly.  As explained in the user guide, this means invoking the weewx daemon, `weewxd`, and passing it a `weewx.conf` configuration file.  In its most general form, it looks like this:

```
weewxd weewx.conf
```

However, the specifics of how this should actually be entered depend on how and where weewx was installed.

### Installations using setup.py

For example, if weewx was installed using setup.py to default location `/home/weewx`, then the following are equivalent:

```
/home/weewx/bin/weewxd /home/weewx/weewx.conf
```

```
cd /home/weewx
./bin/weewxd weewx.conf
```

The first example uses an explicit, absolute path to both `weewxd` and `weewx.conf`, so it can be run from any directory.

The second example shows a `cd` (change directory) to the directory where weewx was installed (`/home/weewx`), followed by relative paths to both the `weewxd` and `weewx.conf`.  The use of `./` in `./bin/weewxd` might not be necessary for a non-root user, but is typically necessary for a root user.  This is because the current directory (`.`) is usually not in the `PATH` for the root user.

If weewx were installed to `/opt/weewx` instead of `/home/weewx`, then the absolute paths would look like this instead:

```
/opt/weewx/bin/weewxd /opt/weewx/weewx.conf
```

### Installations using .deb or .rpm

Now consider the case where weewx was installed from a .deb or .rpm package.  In this case, the following are equivalent:

```
/usr/share/weewx/weewxd /etc/weewx/weewx.conf
```

```
cd /usr/share/weewx
./weewxd /etc/weewx/weewx.conf
```

```
weewxd /etc/weewx/weewx.conf
```

The first example uses an explicit, absolute path to both `weewxd` and `weewx.conf`.

The second example shows a `cd` (change directory) to the directory where the weewx components are installed, followed by a relative path to `weewxd` and an absolute path to `weewx.conf`.  The absolute path is necessary since `weewx.conf` is not in the `/usr/share/weewx` directory hierarchy.

The third example illustrates a special feature of a .deb or .rpm installation.  There is a symbolic link `/usr/bin/weewxd` that points to the actual executable `/usr/share/weewx/weewxd`.  Since `/usr/bin` is in the `PATH` environment variable, one can simply type `weewxd` instead of the full path `/usr/bin/weewxd`, and since it is a symbolic link it runs the actual `weewxd` at `/usr/share/weewx/weewxd`.  

### Other weewx commands

Other weewx commands use a similar pattern.  For example, the `wee_config_devic`e command is used to display and modify options in the hardware.  In addition to requiring a configuration file, it also recognizes many options, such as `--inf`o or `--help`.  The generic description looks like this:

```
wee_config_device /path/to/weewx.conf --help
```

For a setup.py installation, this would be:

```
/home/weewx/bin/wee_config_device /home/weewx/weewx.conf --help
```

For a .deb or .rpm installation, this would be:

```
/usr/share/weewx/wee_config_device /etc/weewx/weewx.conf --help
```

or, even more succinctly:

```
wee_config_device --help
```

The final form is possible because the wee_* commands will look in the standard locations, `/etc/weewx/weewx.conf` and `/home/weewx/weewx.conf`, if no configuration file is specified.