# Goals

To be able to do a complete WeeWX install using `pip`.

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
able to relax this restriction and allow Python 3.5 and 3.6.

Support for Python 2.7 will be dropped. All references to `six` and other
compatibility shims will be removed.

Stretch goal: introduce asynchronous drivers, which would allow more than
one driver to be active at the same time. This feature would probably involve
lots of breaking changes, but it would be a lot more elegant than the present
pattern of using a WeeWX service to add data to the data pipeline.

# Build tools 

There are many tools that were considered to manage the Python distribution
build process including [setuptools](https://setuptools.pypa.io/),
[pdm](https://pdm.fming.dev/), [flit](https://flit.pypa.io), and
[poetry](https://python-poetry.org/). We have chosen poetry to manage
dependencies and to build the packages. It is mature, simple to use, depends on
`pyproject.toml`, and very popular. Poetry can also be used to do an _in situ_
install, but that's not the intended use case. Most users will use `pip`.

The ubiquitous [setuptools](https://setuptools.pypa.io/) was considered, but
poetry is easier to use and its implementation of `pyproject.toml` is more
mature. NB: this only affects the distribution build process. Either way, `pip`
is used to install.


# Applications

Applications will be invoked using an [_entry
point_](https://packaging.python.org/en/latest/specifications/entry-points/).
This is a small shim, installed by `pip`, that invokes a function within a
module or package.

# Resources

The old `distutils` supported a
[`data_files`](https://docs.python.org/3/distutils/setupscript.html#installing-additional-files)
option that could be used to install miscellaneous resources such as
documentation or configuration files into the final installation target.

However, option `data_files` is being deprecated for a variety of reasons (see
[this
post](https://discuss.python.org/t/should-there-be-a-new-standard-for-installing-arbitrary-data-files/7853)
for a summary). Instead, resources must now be included as "package data", that
is, as part of a Python package. That means it will live deep in the target
`site-packages`, where is is difficult to find and even more difficult to
modify.

Furthermore, package data must be regarded as "_read only_". For example, it's
possible that WeeWX could be installed as a zipfile or Python egg, so, even if a
user was willing to find its resources deep in `site-packages` somewhere, s/he
could not edit them.

## Essential resources: `weex.conf` and skins

Both `weewx.conf` and the skins are essential for the proper operation of
WeeWX, so they must be included in the distribution. For reasons outlined above,
they will be put in a dedicated `wee_resources` package as package data.

| Old           | New                        |
|---------------|----------------------------|
| `skins`       | `wee_resources/skins`      |
| `weewx.conf`  | `wee_resources/weewx.conf` |

Once installed, these resources would be copied into position using a utility
(or, possibly, a repurposed `wee_config`).

Unlike previous versions of WeeWX, `weewx.conf` and the `skins`
directory would be updated with every upgrade. This is possible because the
production versions are in a separate location. The production version would
only be touched if the user requests it by using a utility.

## Non essential resources

There is no obvious place to install the documentation and examples using
`pip`. Instead, they could be distributed in a separate tarball, which could
then be downloaded and expanded in a location of the user's choosing.

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

## Definitions

| Name                        | Definition                                                                                                                                                   |
|-----------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| _package resource_          | A resource, such as `weewx.conf` or `skins`, included in a distribution as a _Python package_. Read-only.<br/>Must be accessed using `importlib.resources` . |
| _package resource location_ | Location of the _package resource_, typically somewhere under `site-packages`. May or may not be a file.                                                     |
| _resource root_             | Final, installed, location of the resources, particularly the configuration file and skins.<br/>Typically, `/etc/weewx`, or `/home/weewx`                    |
| _driver_                    | The driver module (e.g., `weewx.drivers.vantage`).                                                                                                           |
| _driver name_               | Returned by the attribute `DRIVER_NAME` of a driver. By default, this becomes the "stanza name" (e.g., `[Vantage]`).                                         |
| _active driver_             | Option `station_type` specifies the active driver to be used. A corresponding _driver name_ must exist.                                                      |

## Subcommand `weectl station`

The utility subcommand `weectl station` manages the station configuration file
and skins.

### Action `station create`

Create a new copy of the configuration file, using the copy of `weewx.conf` in
the _package resource_ as a template. The user will be prompted for optional
values. The default location of the new file will be `/home/weewx/weewx.conf`.

If the configuration file already exists, then an error will be raised.

If they do not already exist, then the skins are also copied over from the
_package resource location_ to the _resource root_.

The following are set, either implicitly or explicitly.

|          Name | What                                                                                 | Default       |
|--------------:|--------------------------------------------------------------------------------------|---------------|
|  `WEEWX_ROOT` | The _resource_root_.                                                                 | `/home/weewx` |
|   `HTML_ROOT` | Where the generated HTML files and images will be put,<br/>relative to `$WEEWX_ROOT` | `public_html` |
|   `SKIN_ROOT` | The directory of the skins, relative to `$HTML_ROOT`.                                | `skins`       |
| `SQLITE_ROOT` | The directory of the SQLite database, relative to `$HTML_ROOT`.                      | `archive`     |
| `station_type`| The active _driver name_.                                                            | `Simulator`   |

#### Option `--config`

Optional path to the configuration file. The value for `HTML_ROOT` will be set
to its directory.

The default is `/home/weewx/weewx.conf`.

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

The default is `/home/weewx/weewx.conf`.

#### Option `--no-prompt`

Do not prompt for values. Obtain new values from the command line only.

### Action `station upgrade`

If necessary, upgrade an existing configuration file. The old version will be
saved as a timestamped copy.

#### Option `--config`

Optional path to the configuration file.

The default is `/home/weewx/weewx.conf`.

### Action `station upgrade-skins`

The skins will be copied over from the _package resource location_ to the
_resource root_ (`$WEEWX_ROOT`). The old version will be saved under a
timestamp.

#### Option `--config`

Optional path to the configuration file.

The default is `/home/weewx/weewx.conf`.




### Examples

```shell
# Create a new weewx.conf, using the vantage driver.
weectl station create --driver=weewx.drivers.vantage

# Same as above, but use a custom configuration file name
weectl station create --driver=weewx.drivers.vantage --config=/home/weewx/house.conf

# Reconfigure it to use a Fine Offset station
weectl station reconfigure --driver=weewx.drivers.fousb --config=/home/weewx/house.conf

# Upgrade its configuration file
weectl station upgrade --config=/home/weewx/house.conf

# Upgrade the skins
weectl station upgrade-skins --config=/home/weewx/house.conf
```

## Subcommand `weectl daemon`

This subcommand manages daemon files.

| Action                    | Flags            | Functionality                               |
|---------------------------|------------------|---------------------------------------------|
| `weectl daemon install`   | `--type=sysv`    | Install `/etc/init.d/weewx`                 |
| `weectl daemon install`   | `--type=systemd` | Install `/etc/systemd/system/weewx.service` |
| `weectl daemon uninstall` | `--type=sysv`    | Remove `/etc/init.d/weewx`                  |
| `weectl daemon uninstall` | `--type=systemd` | Remove `/etc/systemd/system/weewx.service`  |

