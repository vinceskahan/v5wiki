# Overview

This document discusses the goals and design decisions for WeeWX Version 5.

# Goals

Complete WeeWX install, including dependencies, using `pip`, without requiring
root privileges. However, the operating system may require privileges in order
to install daemon files and to access hardware.

Future-proof the install. Right now, we depend heavily on custom commands in
`setup.py`. However, the imperative approach of `setup.py` is being phased out
in favor of a more declarative approach, using `pyproject.toml` (see [PEP
621](https://peps.python.org/pep-0621/) and [PEP
631](https://peps.python.org/pep-0631/)). As an alternative, we could use
`setup.cfg`, but it's clear that `pyproject.toml` is the future.

Python 3.7 or greater. Python 3.7 was released over five years ago (on 27 June
2018), so it should be ubiquitous by now. Python 3.7 is necessary to support
[importing
resources](https://docs.python.org/3/library/importlib.resources.html#module-importlib.resources).
Note that a [backport](https://importlib-resources.readthedocs.io) of
`importlib.resources` is available for earlier versions of Python, so we may be
able to relax this restriction and allow Python 3.6. Version 5 will use
f-strings, so Python 3.5 cannot be supported.

Support for Python 2.7 will be dropped. All references to `six` and other
compatibility shims will be removed. All `__future__` imports will be removed.

Clean separation of WeeWX source code from station data:
 - Easier to back up.
 - Source code can be considered read-only. It could potentially be distributed
   as a zip file or Python egg.
 - Multiple stations can share the same source code. They just have different
   station data areas.

See the section [_Location of user Python code_](#Location-of-user-Python-code) 
below for details.


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

| Non-code resource    | Package data location                     |
|----------------------|-------------------------------------------|
| Configuration file   | `wee_resources/weewx.conf`                |
| Documentation        | `wee_resources/docs`                      |
| Examples             | `wee_resources/examples`                  |
| Skins                | `wee_resources/skins`                     |
| Daemon utility files | `wee_resources/util`                      |
| User code            | `wee_resources/bin/user` (but, see below) |

## Destination for data resources

Previously, we used `/home/weewx` as the destination for station data, but it
required root access in order to set up. With Version 5, the default area
becomes `~/weewx-data` in the user's home directory. However, `/home/weewx`
can continue to be used by older installations.

Because pip no longer allows free access to the operating system, these user
data cannot be set up by pip. Instead, they must be copied into place using a
new tool, `weectl`.

## Install process

All of this means that installing WeeWX becomes a two-step process:

- Install the source code and package data using pip into standard Python
  library locations (usually, `site-packages`).

- Copy package data out of the Python library to a "station data" area using
  `weectl`.

## Install using pip

See the companion document [_pip install strategies_](pip-install-strategies)
for all the various way pip and its allies can be used to install WeeWX.

## Copy non-code resources

With Version 5, the default area for station data is `~/weewx-data`, which allows
WeeWX to be installed entirely without root privileges.

The directory `/home/weewx` served this purpose in previous versions of WeeWX
and, indeed, it can continue to be used by just specifying the location
`/home/weewx/weewx.conf` when using the tool `weectl`. Note that in the past,
`/home/weewx` also held WeeWX code in the `bin` subdirectory, which, starting
with V5, will no longer be used. Instead, all source code will live within the
Python library structure, except for code used by user-installed extensions,
which will live in the station data area.

# Build tools

There are many tools that were considered to manage the Python distribution
build process including [setuptools](https://setuptools.pypa.io/),
[pdm](https://pdm.fming.dev/), [flit](https://flit.pypa.io), and
[poetry](https://python-poetry.org/). We have chosen poetry. It is mature,
simple to use, depends on `pyproject.toml`, and very popular. Poetry can also be
used to do an _in situ_ install, but that's not the intended use case. Most
users will use `pip`.

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

Most packages used by WeeWX already start with the prefix `wee_`, which is a
reasonable claim to a namespace. However, there are two problem areas: `user`
and `schemas`. Renaming them would break a _lot_ of code, particularly the
renaming of `user`.

However, the recommended install method is to install into a virtual environment
where WeeWX does not have to compete with other Python installs for a unique
namespace. A collision is improbable.

# Extensions

Through the years, WeeWX has built up an extensive ecosystem of 3rd party
extensions. It is essential that we not lose backwards compatibility with them.
For this reason, they should continue to be installed directly into the target
installation directories, and not indirectly through package resources. 

All extensions will be installed into the station data area. That is, into 
`~/weewx-data`, or `/etc/weewx`.


# Command `weectl`

Presently, we have 7 different utilities. Version 5 will start the process of
combining them into one command called, simply, `weectl`, with different
subcommands. By having a single application, we make it easier to find the
appropriate subcommand: the user can simply run `weectl --help`.

Version 5 will offer these subcommands:

```
weectl database
weectl debug
weectl device
weectl extension
weectl station
```

## Some examples

```shell
# Create a new station data area in ~/weewx-data (the default)
weectl station create
# Upgrade config file, docs, examples, and daemon utility files in ~/weewx-data.
# Skins will be untouched
weectl station upgrade
# Upgrade the config file only
weectl statioin upgrade --what config
# Upgrade the skins only
weectl station upgrade --what skins

# Install the Windy extension:
weectl extension install https://github.com/matthewwall/weewx-windy/archive/master.zip
```

# Location of user Python code

Previously, user Python code lived amongst the rest of WeeWX's source code, that
is, either under `/home/weewx/bin`, or `/usr/share/weewx`. However, with Version
5, we are seeking a clean separation between WeeWX source code and station data.
Where should its new home be?

Whatever location we use, the parent directory will have to be injected into
`PYTHON_PATH` when `weewxd` starts up. It's the _parent_ directory because
`user` is actually a Python package. We need to find `user.foo`, not just `foo`.

For legacy installs, it is tempting to inject both the legacy location (e.g.,
`/usr/share/weewx/`) and the new location (`/etc/weewx/`), but that tempation
should be resisted. Only _one_, well-defined, location should be used, otherwise
there can be inconsistent versions between the two locations, leading to
difficult-to-diagnose bugs.

We discuss use cases, then possible locations for user Python code, then
draw up the pros and cons.

## Use cases

- New pip install. Everything under `~/weewx-data`.
- Pip install, using legacy (`setup.py`) station data. Station data is located
  under `/home/weewx`. User Python code can be reused if `$USER_ROOT` is
  `./bin/user`.
- New package install. Everything, except the database, is under `/etc/weewx`.
- Package install, using legacy station data. Station data is located under
  `/etc/weewx`. User legacy Python code is in `/usr/share/weewx/user` and cannot
  be reused.
- Run from git repository. 

The table below summarizes the methods and their locations. A question mark
means there are several choices.

| Method     | `$WEEWX_ROOT`  | `$USER_ROOT`            | 
|------------|----------------|-------------------------|
| New pip    | `~/weewx-data` | ?                       |
| Legacy pip | `/home/weewx`  | `./bin/user`            |
| New pkg    | `/etc/weewx`   | ?                       |
| Legacy pkg | `/etc/weewx`   | `/usr/share/weewx/user` |
| Git        | `~/weewx-data` | ?                       |


## Potential locations for `$USER_ROOT`

The following has the pros and cons for different values of `$USER_ROOT`. It only
discusses legacy installs --- new installs will work with any solutoin.

### `$WEEWX_ROOT/bin/user`

Pip install, using legacy user python code, would require no changes.

Package install. Cannot reuse legacy python code. All user extensions would have
to be either reinstalled, or copied from `/usr/share/weewx/user` to the
new, appropriate `$USER_DIR` location, perhaps by the `postinst` script.

### `$WEEWX_ROOT/lib/user`

Has the advantage that the `lib` clearly indicates the role of its underlying
contents: it's part of a library (_not_ some kind of binary). It is also a
little more future-proof --- we have clearly deliniated where user supplied
library-related code goes ---- although at the moment it's difficult to see
where that might be useful.

Pip install would require either a symbolic link from `$WEEWX_ROOT/bin` to
`$WEEWX_ROOT/lib`, or the user would have to copy files over to its new
location.

Package install. The comments for `$WEEWX_ROOT/bin/user` above apply, except
the contents would have to be copied to `/etc/weewx/lib/user`.

### `$WEEWX_ROOT/user`

With this approach, the `user` package would live directly in `$WEEWX_ROOT`. It
is less verbose, but is, perhaps, a bit less future-proof.

Pip installs would require that the `user` subdirectory be moved:

    mv /home/weewx/bin/user/ /home/weewx

This isn't so bad because once the directory `user` had been moved,
`/home/weewx/bin` would no longer be needed and can be safely deleted.


# Definitions

Throughout this document, the following definitions are used

| Name                 | Definition                                                                                                                                                  |
|----------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| _package data_       | A resource, such as `weewx.conf` or `skins`, included in a distribution as a _Python package_. Read-only.<br/>Must be accessed using `importlib.resources`. |
| _package data location_ | Location of the _package data_, typically somewhere under `site-packages`. May or may not be a file.                                                        |
| _WEEWX_ROOT_         | Final, installed, location of the resources, particularly the configuration file and skins.<br/>Typically, `~/weewx-data`, `/etc/weewx`, or `/home/weewx`   |
| _driver_             | The driver module (e.g., `weewx.drivers.vantage`).                                                                                                          |
| _driver name_        | Returned by the attribute `DRIVER_NAME` of a driver. By default, this becomes the "stanza name" (e.g., `[Vantage]`).                                        |
| _active driver_      | Option `station_type` specifies the active driver to be used. A corresponding _driver name_ must exist.                                                     |
