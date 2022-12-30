# Overview

This document discusses the goals and design decisions for WeeWX Version 5.

# Goals

Complete WeeWX install, including dependencies, using `pip`.

No root privileges required to install and manage WeeWX. However, the operating
system may require privileges in order to access hardware.

Future-proof the install. Right now, we depend heavily on custom commands in
`setup.py`. However, the imperative approach of `setup.py` is being phased out
in favor of a more declarative approach, using `pyproject.toml` (see [PEP
621](https://peps.python.org/pep-0621/) and [PEP
631](https://peps.python.org/pep-0631/)). As an alternative, we could use
`setup.cfg`, but it's clear that `pyproject.toml` is the future.

Python 3.7 or greater. Python 3.7 was released over four years ago (on 27 June
2018), so it should be ubiquitous by now. Python 3.7 is necessary to support
[importing resources](https://docs.python.org/3/library/importlib.resources.html#module-importlib.resources).
Note that a [backport](https://importlib-resources.readthedocs.io) of
`importlib.resources` is available for earlier versions of Python, so we may be
able to relax this restriction and allow Python 3.6. WeeWX will use f-strings,
so Python 3.5 cannot be supported.

Support for Python 2.7 will be dropped. All references to `six` and other
compatibility shims will be removed.

Stretch goal: introduce asynchronous drivers, which would allow more than
one driver to be active at the same time. This feature would probably involve
lots of breaking changes, but it would be a lot more elegant than the present
pattern of using a WeeWX service to add data to the data pipeline.

# Resources

The old `distutils` approach used by previous versions of WeeWX supported a
[`data_files`](https://docs.python.org/3/distutils/setupscript.html#installing-additional-files)
option that could be used to install miscellaneous resources, such as
documentation or configuration files, into a final installation target such
as `/home/weewx`.

However, option `data_files` is being deprecated for a variety of reasons (see
[this
post](https://discuss.python.org/t/should-there-be-a-new-standard-for-installing-arbitrary-data-files/7853)
for a summary). Instead, data resources must now be included as "[package
data](https://setuptools.pypa.io/en/latest/userguide/datafiles.html)", that is,
as part of a Python package. Like any other package, package data includes an
`__init__.py` file, but unlike regular packages, it can include non-code
resources.

That means, once installed, these non-code resources will live deep in the
target `site-packages` directory, where they are difficult to find and even more
difficult to modify.

Furthermore, package data must be regarded as "_read only_". For example, it is
possible that WeeWX could be installed as a zipfile or Python egg, so, even if a
user was willing to find the data resources deep in `site-packages` somewhere,
s/he still could not edit them.

## Location of package data

These are the non-code resources that are used in WeeWX and where they will
be located as package data:

| Non-code resource  | Package data location      |
|--------------------|----------------------------|
| Configuration file | `wee_resources/weewx.conf` |
| Skins              | `wee_resources/skins`      |
| Documentation      | `wee_resources/docs`       |
| Examples           | `wee_resources/examples`   |

## Destination for data resources

Previously, we used `/home/weewx` as the destination for user data, but it
requires root access in order to set up. With Version 5, the default area
becomes `~/weewx-data` in the user's home directory. However, `/home/weewx`
can continue to be used by older installations.

Because pip no longer allows free access to the operating system, these user
data cannot be set up by pip. Instead, they must be copied into place using a
new tool, `weectl`.

## Install process

All of this means that installing WeeWX becomes a two-step process:

- Install using pip. This installs the code base and package data into the
  Python library.

- Copy package data out of the Python library to a "user data" area using
  `weectl`.

## Install using pip

See the companion document [_pip install strategies_](pip-install-strategies.md)
for all the various way pip and its allies can be used to install WeeWX.

## Copy non-code resources

With Version 5, the default area for user data is `~/weewx-data`, which allows
WeeWX to be installed entirely without root privileges.

The directory `/home/weewx` served this purpose in previous versions of WeeWX
and, indeed, it can continue to be used by just specifying the location
`/home/weewx/weewx.conf` when using the tool `weectl`. Note that in the past
`/home/weewx` also held WeeWX code in the `bin` subdirectory, which, starting
with V5, will no longer be used. With V5, all source code lives within the
Python environment.


# Build tools

There are many tools that were considered to manage the Python distribution
build process including [setuptools](https://setuptools.pypa.io/),
[pdm](https://pdm.fming.dev/), [flit](https://flit.pypa.io), and
[poetry](https://python-poetry.org/). We have chosen poetry. It is mature,
simple to use, depends on `pyproject.toml`, and very popular. Poetry can also
be used to do an _in situ_ install, but that's not the intended use case.
Most users will use `pip`.

The ubiquitous [setuptools](https://setuptools.pypa.io/) was considered, but
poetry is easier to use and its implementation of `pyproject.toml` is more
mature. NB: this only affects the distribution build process. Either way, `pip`
is used to install.

# Applications

Applications will be invoked using an [_entry
point_](https://packaging.python.org/en/latest/specifications/entry-points/).
This is a small shim, installed by `pip`, which invokes a function within a
module or package.

# Namespaces

Generally, we expect WeeWX to be installed in a virtual envirornment, where the
names of its modules and packages would not collide with another application.
Nevertheless, it is possible that the user would install to the system's
`sys.exec`, and we should be prepared for this.

Most packages used by WeeWX already start with the prefix `wee_`, which is a
reasonable claim to a namespace. However, there are two problem areas:

| Old       | New           |
|-----------|---------------|
| `user`    | `wee_user`    |
| `schemas` | `wee_schemas` |

Note that these will break a _lot_ of code, particularly the renaming of `user`.
Fortunately, all references to the `user` or `schemas` packages are dynamic,
that is, they are in the form of string options in `weewx.conf` or `skin.conf`.
The actual instantiation is done by the WeeWX function `weeutil.get_object()`.
This makes it straightforward to maintain backwards compatibility by having
`get_object()` remap references from `user` to `wee_user` at runtime. In a
similar manner, references to `schemas` would get remapped to `wee_schemas`. All
of this would be transparent to the user.

# Extensions

Through the years, WeeWX has built up an extensive ecosystem of 3rd party
extensions. It is essential that we not lose backwards compatibility with them.
For this reason, they should continue to be installed directly into the target
installation directories, and not indirectly through package resources.

# Command `weectl`

Presently, we have 7 different utilities. Version 5 will combine these into one
command called, simply, `weectl`, with different subcommands. By having a
single application, we make it easier to find the appropriate command: the user
can simply run `weectl --help`.

## Subcommand `weectl station`

The utility subcommand `weectl station` manages the station configuration file
and skins and can set up new user data areas.

### Action `station create`

Create a new copy of the configuration file, using the copy of `weewx.conf` in
the _package data_ as a template. The user will be prompted for optional
values. The default location of the new file will be `~/weewx-data/weewx.conf`.

If the configuration file already exists, then an error will be raised.

If they do not already exist, then the skins are also copied over from the
_package data_ to the.

The following are set, either implicitly or explicitly.

|           Name | What                                                                                 | Default        |
|---------------:|--------------------------------------------------------------------------------------|----------------|
|   `WEEWX_ROOT` | The _resource_root_.                                                                 | `~/weewx-data` |
|    `HTML_ROOT` | Where the generated HTML files and images will be put,<br/>relative to `$WEEWX_ROOT` | `public_html`  |
|    `SKIN_ROOT` | The directory of the skins, relative to `$HTML_ROOT`.                                | `skins`        |
|  `SQLITE_ROOT` | The directory of the SQLite database, relative to `$HTML_ROOT`.                      | `archive`      |
| `station_type` | The active _driver name_.                                                            | `Simulator`    |

#### Option `--config`

Optional path to the configuration file. The value for `WEEWX_ROOT` will be set
to its directory.

The default is `~/weewx-data/weewx.conf`.

#### Option `--driver`

Set the configuration file up to use the given _driver_.

Default is `weewx.drivers.simulator`.

#### Option `--skin_root`

Optional path to where the skins will be installed, relative to `$WEEWX_ROOT`.

Default is `skins`.

#### Option `---html_root`

Optional path to where the generated HTML files and plots will be placed,
relative to `$WEEWX_ROOT`.

Default is `public_html`.

#### Option `--no-prompt`

Do not prompt for values. Obtain new values from the command line only.

### Action `station reconfigure`

This command provides an opportunity to reprovision an existing configuration
file.

#### Option `--driver`

Reconfigure the configuration file to use the given _driver_.

Default is to use the existing _driver_.

#### Option `--config`

Optional path to the configuration file.

The default is `~/weewx-data/weewx.conf`. If that doesn't exist, then 
`/etc/weewx/weewx.conf` will be tried. If that doesn't exist, then
`/home/weewx/weewx.conf` will be tried.

#### Option `--no-prompt`

Do not prompt for values. Obtain new values from the command line only.

### Action `station upgrade`

If necessary, upgrade an existing configuration file. The old version will be
saved as a timestamped copy.

#### Option `--config`

Optional path to the configuration file.

The default is `~/weewx-data/weewx.conf`. If that doesn't exist, then 
`/etc/weewx/weewx.conf` will be tried. If that doesn't exist, then
`/home/weewx/weewx.conf` will be tried.


### Action `station upgrade-skins`

The skins will be copied over from the _package resource location_ to the
_resource root_ (`$WEEWX_ROOT`). The old version will be saved under a
timestamp.

#### Option `--config`

Optional path to the configuration file.

The default is `~/weewx-data/weewx.conf`. If that doesn't exist, then 
`/etc/weewx/weewx.conf` will be tried. If that doesn't exist, then
`/home/weewx/weewx.conf` will be tried.

### Examples

```shell
# Create a new weewx.conf in ~/weewx-data, using the vantage driver.
weectl station create --driver=weewx.drivers.vantage

# Same as above, but create in the alternative directory ~/alt-dir
weectl station create --driver=weewx.drivers.vantage --config=~/alt-dir/house.conf

# Reconfigure the configuration file in the default area to use a Fine Offset station
weectl station reconfigure --driver=weewx.drivers.fousb

# Upgrade its configuration file
weectl station upgrade

# Upgrade its skins
weectl station upgrade-skins
```

## Subcommand `weectl daemon`

This subcommand manages daemon files.

| Action                    | Flags            | Functionality                               |
|---------------------------|------------------|---------------------------------------------|
| `weectl daemon install`   | `--type=sysv`    | Install `/etc/init.d/weewx`                 |
| `weectl daemon install`   | `--type=systemd` | Install `/etc/systemd/system/weewx.service` |
| `weectl daemon uninstall` | `--type=sysv`    | Remove `/etc/init.d/weewx`                  |
| `weectl daemon uninstall` | `--type=systemd` | Remove `/etc/systemd/system/weewx.service`  |

 # Definitions

Throughout this document, the following definitions are used

| Name                 | Definition                                                                                                                                                  |
|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| _package data_       | A resource, such as `weewx.conf` or `skins`, included in a distribution as a _Python package_. Read-only.<br/>Must be accessed using `importlib.resources`. |
| _package data location_ | Location of the _package resource_, typically somewhere under `site-packages`. May or may not be a file.                                                    |
| _WEEWX_ROOT_         | Final, installed, location of the resources, particularly the configuration file and skins.<br/>Typically, `~/weewx-data`, `/etc/weewx`, or `/home/weewx`    |
| _driver_             | The driver module (e.g., `weewx.drivers.vantage`).                                                                                                          |
| _driver name_        | Returned by the attribute `DRIVER_NAME` of a driver. By default, this becomes the "stanza name" (e.g., `[Vantage]`).                                        |
| _active driver_      | Option `station_type` specifies the active driver to be used. A corresponding _driver name_ must exist.                                                     |
