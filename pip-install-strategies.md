The Python tool [pip](https://pip.pypa.io) is a very powerful and flexible
resource for installing libraries and applications. However, all that
flexibility can lead to a bewildering set of choices for how to use it.

This document outlines some of these choices, as well as their pros and cons.
While its context is installing WeeWX, most conclusions would apply to any
package or application.

## System install

With this strategy, WeeWX is installed using `sudo pip` as a system-wide
resource,
usually in a location such as `'/usr/lib/python3.x/dist-packages` or, possibly,
`/usr/lib/python3/dist-packages/`.

```sh
sudo python3 -m pip install weewx
```

### Advantages

- Simple install, resulting in a simple state to understand: everything is in
  one, well-known location.
- Installs system-wide, so any user, including root, has access to it.

### Disadvantages

- Requires root privileges.
- Because this approach can result in one or more of the packages used by WeeWX
  getting updated, it can break other programs, which may be depending on
  specific versions being available.
- If you ever remove WeeWX, you will be faced with the choice of whether to
  remove its prerequisites. You may not know, or remember, whether other
  software depends on them.

## User install

By adding a `--user` flag, `pip` installs WeeWX in a subdirectory in the user's
home directory. You will also end up with this method if you forget the `sudo`
in a systems install.

```shell
python3 -m pip install weewx --user
```

### Advantages

- Does not require root privileges.
- Will not break existing systems programs.
- While not as simple as a system install, it's still pretty simple: system
  resources remain untouched, while the WeeWX resources end up under the user's 
  home directory.

### Disadvantages

- Other users will not be able to run the results, at least without patching
  `PYTHONPATH`, because WeeWX will be located under a user's home directory.
- This means WeeWX cannot be run as a daemon without some crafting of
  `PYTHONPATH`.
- To remove WeeWX, you will have to explicitly remove it and each of its 
  dependencies using `pip uninstall`.

## Install in a virtual environment

This is a multi-step process. Depending on your environment, you may have to
install the Python package `venv` first.

```shell
cd ~
# Create a virtual environment in weewx-venv
python3 -m venv weewx-venv
# Activate it
source weewx-venv/bin/activate
# Then install WeeWX in it
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

- More complicated and not as easy to understand what's happening.

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
