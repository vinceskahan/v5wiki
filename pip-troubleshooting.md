# Troubleshooting pip installs

Normally, when you install WeeWX with pip, it arranges to not only install WeeWX, but also its
dependencies. Because WeeWX itself is a pure Python program, it is pretty easy to install, but its
dependencies may not be.

Try installing WeeWX the suggested way

```
python3 -m pip install weewx --user
```

If that doesn't work, use this guide for suggestsions on getting around common problems.


## Install piecemeal

One approach is to have pip install WeeWX _without_ its dependencies, then manually install each
dependency, possibly working around any problems that might come up:

```shell
python3 -m pip install weewx --no-deps
python3 -m pip install configobj  
python3 -m pip install pyserial
python3 -m pip install pyusb
python3 -m pip install CT3
python3 -m pip install ephem
python3 -m pip install Pillow
python3 -m pip install pymysql[rsa]
```

On well-known platforms, pre-built "wheels" probably exist for each of these dependencies, which
pip will download and use. A _wheel_ is a _built distribution_. That is, it targets a specific
hardware architecture, operating system, Python version, etc. Most importantly, it includes
pre-built binaries, obviating the need for pip to compile the library for the target platform.

For example, Pillow, one of WeeWX's dependencies, has 65 prebuilt wheels! They cover everything
from Python 3.7 on Windows to Python 3.11 on the Mac. (By contrast, WeeWX, a pure-Python program,
has one --- the same version will run on any platform, and any version of Python 3.)

If you have an unusual platform, a wheel may not exist for one or more of these dependencies. In
this case, pip will attempt to create a wheel, which will involve compiling any required binaries.
It may or may not succeed.

Here's how to work around some of the more common problems.

### configobj

`configobj` rarely causes any problems. It has only one wheel, which works on all versions of
Python 3.

### pyserial

`pyserial` rarely causes any problems.  It has only one wheel, which works on all versions of
Python 3. If your hardware has a USB interface, you may not even need it.

### pyusb

`pyusb` rarely causes any problems.  It has only one wheel, which works on all versions of
Python 3. If your hardware has a serial interface (such as the Davis station), you may not even
need it.

### CT3

The library `CT3` is the Cheetah library (formerly called `Cheetah3`). It has 25 wheels, which will
cover most cases. However, if you have an unusual platform, pip will attempt a `setup.py` install,
which involves compiling its "NameMapper" utility. It's a simple utility, so this usually succeeds
without problem. If not, Cheetah has a fallback of a pure Python, but slower, version.

### ephem

The library `ephem` has 66 wheels, which cover most situations. For the few cases that a wheel is
not available, pip will try to build one for the target platform. This involves compiling many
small C programs, but they are simple, with no dependencies, so this rarely causes a problem.

If all else fails, `ephem` is optional and can be left out, but you'll miss some nice almanac
information.

### Pillow

Despite its abundance of wheels (65), installing Pillow often causes problems because it may be
missing an underlying library. By default, it needs `zlib`, and `libjpeg`, which may or may not be
available on your platform (`zlib` is necessary to create the `.png` files WeeWX uses for plots).
Usually, installing them using `apt` (or whatever is appropriate for your platform) will solve the
problem. See the webpage [Installation](https://pillow.readthedocs.io/en/latest/installation.html)
on the Pillow website for specific instructions.

In some cases, you might have to downgrade pip or Pillow to get it to install. For example, on
a 32-bit version of Debian 9 running Python 3.7.5, installing the latest version of Pillow, 9.5.0,
continually gave errors that the `zlib` header files and/or library were missing, despite
`zlib1g-dev` having been installed. In the end, installing a downgraded version of Pillow
worked for me:

```shell
python3.7 -m pip install pillow=9.4.0
```

### pymysql

This is the client library necessary to use MySQL. If you are not using that database, then you do
not need to install `pymysql`. 

While installing the library itself is straightforward, if you are using `sha256_password` or
`caching_sha2_password` authentication then you will need to also install a rsa library. This
usually involves installing `cryptography`, which, if it does not exist on your platform, can be
very difficult to do. It requires not only a C compiler and various headers, but also a Rust
compiler.

If compiling `cryptography` is not possible, you will have to use either some other kind of
authentication, or another database.
