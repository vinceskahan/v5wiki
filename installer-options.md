# Installer options

WeeWX started with a non-standard use of python's `setup.py`. The intent was to put all of the
WeeWX bits in a single directory - installed code, configurations, skins, and data. That is not how
installation of a 'standard' python module works, so `setup.py` and `setup.cfg` had to be modified
to make them behave the way we wanted.

Support was later added for per-platform packages, such as `deb` for Debian, Ubuntu, and Mint as
well as `rpm` for Redhat/CentOS and SuSE.

These notes about the pros/cons of different mechanisms, actual and potential, for installing
WeeWX.
## General

The present (weewx 4 and earlier) system of supporting both `setup.py` installs and package installers results in many different configurations. This means interrogating users about their install method
before we can answer a support question.  Although there are only two families: python install (setup.py) or platform install (apt, yum, zypper), within each of these there are can be many variants.  For example, a python install might not be in /home/weewx, and an apt install might have multiple conf files (e.g., for weewx-multi).  A single weewx code install could be run using either python2 or python3.  A setup.py install could be run within a `venv` or other python virtualization.  Any of these could be run within a virtual environment or within a container, or within WSL.

Python installs cannot integrate fully with the system (init system, logrotate, syslog, logwatch), at least not a pip or venv

The creation of platform packages is highly dependent on bespoke scripts written in Perl by Matthew. We're screwed if he gets hit by a bus, or until Tom decides to learn PERL (or at least wear a hazmat suit when he looks at the code).

OTOH, the `setup.py` method installs in non-standard places. It also requires the user to install
dependencies manually. This is a major reason why we have stuck with the same dependencies for over
10 years, despite a revolution in potential supporting libraries (such as `requests` or `tzinfo`).

There is a fundamental issue that weewx has ignored: separation of code and data.  The weewx implementation has always done this in its implementation, but it has not had separation when it comes to installation and upgrade.  Many of the installation problems would go away and simplify if weewx code and weewx data/config were treated separately for installation and upgrade.  For example, installation could be a two-step process: first install weewx, then create a station configuration.  A station configuration consists of a conf file, database, and skins.

The following sections enumerate the existing (weewx 4) installation options, as well as some alternatives.

## weewx-ified setup.py (single directory install)

Pros

+ easy, but with caveats: `sudo python3 setup.py install`
+ everything in a single directory
+ easy to maintain multiple concurrent versions for development
+ Root privileges not required for install, possibly required to run

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
  For a simple out-of-the-box experience, we should install a udev file that recognizes all supported (known?) hardware, with `/dev` links and non-root permissions set so that everything just works.
- entry points.  Pip installs entry points in a separate `bin` directory within whichever python the pip is associated. Modern
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
- you must install weewx for each python environment.  this could be a good thing, could be a bad thing.  when the weewx configs and data are considered part of weewx, this is a bad thing.

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

## The ctl pattern

This is a proposal for weewx 5.  In the ctl pattern, the weewx code and weewx data are separated during the installation and upgrade processes.  The first step would be to install weewx code.  This would *not* install a config file (or database, although database is lazy creation anyway).  It would install a template config, and template skins, i.e., the config and skins that would be used to create a station instance.  The second step would be to create a station instance.  This would create a config file and skins.  If config and skins already exist, then this is a no-op, or perhaps an option to upgrade.  It would still be possible to make the end-user experience a single step, but by separating these into different logical (and physical) tasks we can address some of the problems in the installation and upgrade of weewx 4 and earlier.

This requires a tool, say `wee_ctl`, for the post-install operations.  This tool would do things like create a station instance, upgrade a station instance, start/stop a station instance, install the init file(s).  It would exist alongside the existing `wee_xxx` utilities.  (Perhaps there would be a unifying command that passes to these, in the same way that 'git' is a single entry point that invokes the lower-level commands that do the actual work)

Here is a clean install using pip:
```
# install weewx using pip
pip install weewx
# create a station instance called 'weewx' with default settings
wee_ctl create weewx
```


