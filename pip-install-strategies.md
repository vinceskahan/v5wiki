This document outlines the pros and cons of different ways of installing WeeWX
with pip.

## System install

This is the default install method for `pip`. WeeWX is installed as a
system-wide resource, usually in a location such as
`'/usr/lib/python3.x/dist-packages` or, possibly,
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

- Because this approach can result in one or more of WeeWX's required packages
  getting updated, it can break other programs, which may be depending on
  specific versions being available.

- If you ever remove WeeWX, you will be faced with the choice of whether to
  remove its prerequisites. You may not know, or remember, whether other
  software depends on them.

## User install

By adding a `--user` flag, `pip` installs WeeWX in a subdirectory in the user's
home directory.

You will also end up with this method if you forget the `sudo` in a systems
install.

```shell
python3 -m pip install weewx --user
```

### Advantages

- Does not require root privileges.
- Will not break existing systems programs.
- While not as simple as a sysstem install, it's still pretty simple: system
  resources end up in one place, user resources end up under the user's home
  directory.

### Disadvantages

- Other users will not be able to run it, at least without patching
  `PYTHONPATH`, because WeeWX will be located under a user's home directory.
- This means WeeWX cannot be run as a daemon without some crafting of
  `PYTHONPATH`.

## Install in a virtual environment

This is a multi-step process. Depending on your environment, you may have to
install `venv` first.

```shell
cd ~
python3 -m venv weewx-venv
source weewx-venv/bin/activate
python3 -m pip install weewx 
```

### Advantages

- Does not require root privileges.
- Everything ends up in one spot (`~/weewx-venv` in the example above).
- Does not break existing code.
- Other users can run and use the results
- You can simply delete the virtual environment to erase what you've done

### Disadvantages

- More complicated and not as easy to understand what's happening.

## Use `pipx`

This method uses the tool [`pipx`](https://pypa.github.io/pipx/) to install
WeeWX in a single step into a virtual environment. You may have to install 
`pipx` first.

```shell
pipx install weewx
```

### Advantages

- Simple install method.
- No need to remember to "activate" the virtual environment --- just run
`weewxd` as usual.
- Does not break existing code.
- Other users can run and use the results

### Disadvantages

- A little harder to understand what just happened.
