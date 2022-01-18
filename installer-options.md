# Installer options

WeeWX started with a non-standard use of python's `setup.py`. The intent was to put all of the
WeeWX bits in a single directory - installed code, configurations, skins, and data. That is not how
installation of a 'standard' python module works, so `setup.py` and `setup.cfg` had to be modified
to make them behave the way we wanted.

Support was later added for per-platform packages, such as `deb` for Debian, Ubuntu, and Mint as
well as `rpm` for Redhat/CentOS and SuSE.

These notes about the pros/cons of different mechanisms, actual and potential, for installing
WeeWX.

## weewx-ified setup.py (single directory install)

Pros

+ easy, but with caveats: `sudo python3 setup.py install`
+ everything in a single directory
+ easy to maintain multiple concurrent versions for development
+ Root privileges not required.

Cons

- Does not manage dependencies.
- manual configuration for init.d/systemd
- manual configuration for ancillary configs
- full path or setting of PATH required to run executables.

## pip

Pros

+ easy and it's what Python users expect: `pip install weewx`
+ works great for pure python apps
+ works with python2 or python3
+ Manages dependencies
+ works on any platform on which python runs
+ mechanisms for system-wide or per-user install
+ works well with virtual environments

Cons

- no auto-install mechanism for init.d/systemd/rc.
- no mechanism for udev scripts.<br/>
  Not sure why we'd need this.
- entry points are buried with other python commands. <br/>
  Don't know what this means. Pip installs entry points in a separate `bin` subdirectory. Modern
  implementations of `setuptools` can also
  create [shell wrappers](https://setuptools.pypa.io/en/latest/userguide/entry_point.html) around
  entry points.
- where do the config files and skins live?<br/>
  The tool [Ansible](https://www.ansible.com/) puts them right in with the libraries. In our case,
  that might mean directly under the `weewx` package
- where do the data live?<br/>
  This as a bigger problem. It feels unnatural to put the database in amongst the modules,
  but `/var/lib` requires root privileges. What about `~/.weewx`?
- no standard for ancillary configs such as logrotate, rsyslog, udev, nginx
- no mechanism for installing multiple concurrent versions<br/>
  This can be done with separate virtual environments.

## platform packages

Pros

+ easy: `sudo yum install weewx` or `sudo apt install weewx`
+ for users, it looks like any other application
+ Manages dependencies
+ configs live in `/etc/weewx`
+ data live in `/var/lib/weewx`
+ ancillary configs live in `/etc/weewx` and just need symlink to activate
+ entry points in `/usr/bin` so no `PATH` futzing required

Cons

- requires root to install
- requires tweaks to run as non-root user (this could be fixed)
- Requires maintenance of many package installers, all with bespoke scripts, written in Perl.
- Many dependencies take forever before they appear in the installer stream. A Python 3 version of
  Cheetah took about 3 years, yet it appeared in pypi almost immediately.

## General

The present system of supporting both `setup.py` installs and package installers results in many
possible different final configurations. This means interrogating users about their install method
before we can answer a support question.

It is also highly dependent on bespoke scripts written in Perl by Matthew. We're screwed if he gets
hit by a bus.

OTOH, the `setup.py` method installs in non-standard places. It also requires the user to install
dependencies manually. This is a major reason why we have stuck with the same dependencies for over
10 years, despite a revolution in potential supporting libraries (such as `requests` or `tzinfo`).