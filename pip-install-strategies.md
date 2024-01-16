The Python tool [pip](https://pip.pypa.io) is a very powerful and flexible
resource for installing libraries and applications. However, all that
flexibility can lead to a bewildering set of choices for how to use it.

This document outlines some strategies for installing WeeWX Version 5 using pip,
along with their various pros and cons. While its context is installing WeeWX,
most conclusions would apply to any package or application.

## Install in a virtual environment (preferred)

This is the recommended install strategy. You can find more details in the [pip
"quickstart" guide](https://www.weewx.com/docs/5.0/quickstarts/pip/). What
follows is just a general outline, for purposes of comparing to other install
strategies.

This is a multi-step process. Depending on your environment, you may have to
install the Python package `venv` first.

```shell
cd ~
# Create a virtual environment in weewx-venv
python3 -m venv weewx-venv
# Activate it
source weewx-venv/bin/activate
# Then, using pip, install WeeWX into it
python3 -m pip install weewx 
```

### Advantages

- Does not require root privileges.
- Will not break existing systems programs.
- Other users can run and use the results.
- Everything ends up in one spot (`~/weewx-venv` in the example above).
- You can simply delete the virtual environment when you're done with it.
- Does not require any tools other than what's in a standard Python environment.

### Disadvantages

- More complicated.
- If you forget to "activate" your virtual environment, the shell will not be able to find any of
  the WeeWX executables.
- Any daemon files will need the explicit path to `weewxd`, which is located inside the virtual
  environment.


## System install

With this strategy, WeeWX is installed using `sudo pip` as a system-wide resource. The WeeWX
libraries end up in a location such as `/usr/local/lib/python3.x/dist-packages/` or, possibly,
`/usr/lib/python3/dist-packages/`, while the executables are in `/usr/local/bin`. All are
accessible to any user.

```shell
sudo python3 -m pip install weewx
```

### Advantages

- Simple install, resulting in a state that is simple to understand: everything 
  is in one, well-known location.
- Installs system-wide, so any user, including root, has access to it.

### Disadvantages

- Requires root privileges.
- Because this approach can result in one or more of the packages used by WeeWX
  getting updated, it can break other programs that are depending on particular 
  versions of the same packages.
- For this reason, the latest versions of pip will declare an error, 
  "**error: externally-managed-environment**". See [PEP 668](https://peps.python.org/pep-0668/).
- If you ever remove WeeWX, you will be faced with the choice of whether to
  remove its dependencies. You may not know, or remember, whether other
  programs depend on them.
- To remove WeeWX, you will have to explicitly remove it and each of its 
  dependencies using `pip uninstall`.


## User install

By adding a `--user` flag, `pip` installs WeeWX and its dependencies into
sudirectories of
[`site.USER_BASE`](https://docs.python.org/3/install/index.html#alternate-installation-the-user-scheme),
typically `~/.local` on Unix machines, including macOS.

You will also end up with this method if you forget the `sudo` in a systems
install.

```shell
# Either...
python3 -m pip install weewx --user
# ... or (note sudo is not being used):
python3 -m pip install weewx
```

### Advantages

- Does not require root privileges.
- Is less likely to break existing systems programs.
- While not as simple as a system install, it's still pretty simple: system
  code remains untouched, while WeeWX code and resources end up under the
  user's home directory.

### Disadvantages

- Because packages installed in a "user" area typically come earlier in the Python path, a
  `--user` install can still break other packages by "shadowing" a package installed system-wide.
- For this reason the latest versions of pip will still declare an error, 
  "**error: externally-managed-environment**". See [PEP 668](https://peps.python.org/pep-0668/).
- If you run a daemon as `root`, it will be unable to find the WeeWX libraries
  unless you patch `PYTHONPATH` (because they are located under `~/.local`
  instead of `/usr/lib`). However, this problem goes away if you run
  the daemon as the user.
- To remove WeeWX, you will have to explicitly remove it and each of its 
  dependencies using `pip uninstall`.


## Install using `pipx`

This method uses the tool [`pipx`](https://pypa.github.io/pipx/) to install
WeeWX in a single step into a virtual environment that `pipx` creates. The
virtual environment is typically located at something like
`~/.local/pipx/venvs/weewx`.

```shell
# You may have to install `pipx` first:
python3 -m pip install --user pipx
# Then using pipx, install weewx
pipx install weewx
```

### Advantages

- Does not require root privileges.
- Will not break existing systems programs.
- Other users can run and use the results.
- No need to remember to "activate" the virtual environment --- just run
  `weewxd` as usual.

### Disadvantages

- Another tool is involved.
- A little harder to understand what's happening.
- The tool must be used to remove WeeWX when you're done with it (although 
  it's just a single step).
- If you install extensions that require dependencies, you cannot simply
  install them by using `pip install`. Instead, you must ask `pipx` to "inject"
  them. For example, if an extension used the popular `requests` package,
  it would have to be installed using `pipx inject weewx requests`.


## Install using poetry

WeeWX uses the tool [Poetry](https://python-poetry.org/) to manage the process
of creating wheels and tarballs from the source tree. However, it can also
be used to install the required packages in a virtual environment. This is most
useful when working out of the git repository.

You will have to install Poetry first. Then

```shell
# cd to the weewx git repository. For example
cd ~/git/weewx
# Install dependencies in a virtual environment
poetry install
# Activate it
poetry shell
```

Now any commands you run (such as `weewxd`) will be done in the virtual
environment. It's possible to get the location of that environment by using the
`poetry env info` command, which would then allow it to be used by a daemon.

### Advantages

- Does not require root privileges.
- Will not break existing systems programs.
- Other users can run and use the results.
- Allows an isolated environment when running out of the git repository.

### Disadvantages

- Another tool is involved.
- Harder to understand what's happening.
