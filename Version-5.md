# Goals

To be able to do a complete WeeWX install using `pip`.

Future-proof the install. Right now, we depend heavily on custom commands in
`setup.py`. However, the imperative approach of `setup.py` is being phased out
in favor of a more declarative approach, using `pyproject.toml` (see [PEP
621](https://peps.python.org/pep-0621/) and [PEP
631](https://peps.python.org/pep-0631/)). As an alternative, we could use
`setup.cfg`, but it's clear that `pyproject.toml` is the future.

Python 3.5 or greater. Python 3.5 was released 13 Sept 2015, so it should be
ubiquitous by now. Support for Python 2.7 will be dropped. All references
to `six` and other compatibility shims would be removed.

Stretch goal: introduce asynchronous drivers, which would allow more than
one driver to be active at the same time. This feature would probably involve
lots of breaking changes, but it would be a lot more elegant than the present
pattern of using a WeeWX service to add data to the data pipeline. Asynchronous
drivers would require Python 3.7 or greater.

# Build tools 

There are many tools that were considered to manage the build process including
[setuptools](https://setuptools.pypa.io/), [pdm](https://pdm.fming.dev/),
[flit](https://flit.pypa.io), and [poetry](https://python-poetry.org/). We have
chosen poetry to manage dependencies and to build the packages. It is mature,
simple to use, depends on `pyproject.toml`, and very popular. Poetry can also be
used to do an _in situ_ install, but that's not the intended use case. Most
users will use `pip`.

The ubiquitous [setuptools](https://setuptools.pypa.io/) was considered, but
poetry is easier to use and its implementation of `pyproject.toml` is more
mature. NB: this only affects the build process. Either way, `pip` is used to
install.


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

# Utility `weewx`

Presently, we have 7 different utilities. Version 5 will combine these into one
utility called, simply, `weewx`, with 7 different subcommands. 

| Command     | Old utility     | Functionality                         |
|-------------|-----------------|---------------------------------------|
| `config`    | `wee_config`    | Copy resources to target directories  |
| `database`  | `wee_database`  | Manage database                       |
| `debug`     | `wee_debug`     | Create debug document                 |
| `device`    | `wee_device`    | Information and management of devices |
| `extension` | `wee_extension` | Install and remove extensions         |
| `import`    | `wee_import`    | Import foreign data                   |
| `report`    | `wee_reports`   | Generate a report                     |

By having a single utility, we make it easier to find the appropriate command:
The user can simply run `weewx --help`. 