## Working with paths in a Unix environment

Understanding how paths work is a rather important part of working with the command-line on any Linux/BSD/Unix system.  The `PATH` environment variable tells the system where to look for commands.  It is a colon-delimited list of paths.  In the case of weewx, there are typically two paths that matter most: the path to the command and the path to the `weewx.conf` configuration file.

Let's start with a basic weewx operation: running weewx directly.  As explained in the user guide, this means invoking the weewx daemon, `weewxd`, and passing it a `weewx.conf` configuration file.  In its most general form, it looks like this:

```shell
weewxd weewx.conf
```

However, the specifics of how this should actually be entered depend on how and where weewx was installed.

### Installations using DEB or RPM package

Consider the case where weewx was installed from a .deb or .rpm package.  In this case, the following are equivalent:

```shell
/usr/bin/weewxd /etc/weewx/weewx.conf
```

```shell
cd /usr/bin
./weewxd /etc/weewx/weewx.conf
```

```shell
weewxd /etc/weewx/weewx.conf
```

The first example uses an explicit, absolute path to both `weewxd` and `weewx.conf`.

The second example shows a `cd` (change directory) to the directory where the weewxd command is installed, followed by a relative path to `weewxd` and an absolute path to `weewx.conf`.

The third example illustrates a special feature of a .deb or .rpm installation.  There is a symbolic link `/usr/bin/weewxd` that points to the actual executable `/usr/share/weewx/weewxd`.  Since `/usr/bin` is in the `PATH` environment variable, one can simply type `weewxd` instead of the full path `/usr/bin/weewxd`.  

### Installations using `pip`

Consider the case where WeeWX was installed using `pip`.  In this case, the following will fail:

```
weewxd /etc/weewx/weewx.conf
```

It fails because `weewxd` is not in the PATH.  The `weewxd` and `weectl` commands are in the Python virtual environment that was created as a part of the `pip` installation.  In order to access those commands, you must put them into the PATH.  This is what the virtual environment `activate` command does.  For example, if you installed the virtual environment at `~/weewx-venv`, then the environment is activated like this:

```
source ~/weewx-venv/bin/activate
```
Then the `weewxd` command will be found.  The `activate` only has to be done once in a terminal window or login shell - it does not have to be entered before every WeeWX command.

### Other WeeWX commands

The pattern described above applies to both of the WeeWX commands, `weewxd` and `weectl`.  For example, the `weectl device` command is used to display and modify options in the hardware.  In addition to requiring a configuration file, it also recognizes many options, such as `info` or `--help`.  The generic invocation looks like this:

```shell
weectl device /path/to/weewx.conf --help
```

or, even more succinctly:

```shell
weectl device --help
```

The final form is possible because the WeeWX commands will look in the standard locations, `/etc/weewx/weewx.conf` and `~/weewx-data/weewx.conf`, if no configuration file is specified.
