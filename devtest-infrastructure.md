# Development and testing infrastructure

## Packaging

Supported packages:

* source tarball (typically built on macOS)
* debian .deb (built on debian6)
* redhat .rpm (built on CentOS5)
* suse .rpm (built on SUSE12)
* debian apt repository (maintained on debian7)

Preparation of a weeWX release is done natively for each supported platform.  This has been done on x86_64 virtual machines for each of the supported packages.  Each of these virtual machines contains the minimal installation required to build and package weeWX for that platform.

## Testing

Pre-release testing consists of:

* running the weeWX unit tests in a development environment
    * (what are the current unit tests and what do they measure?)
* doing an install of each supported package on a virgin system
    * (also do setup.py install on each?)
* doing an upgrade of each supported package to a system with the previous weeWX version already installed
    * (how do you define 'previous'?)

## Misc questions

* (need) what is the current release process, how is it triggered, what does it generate ?
* (need) what other process(es) are you thinking of improving or adding ?

## How can we improve the process?

* (vince suggestions)
    * autobuilds
        * create each of the packages hands-off automatically based on some github hook
        * also be able to trigger them manually
        * provide a repo of non-tgz package'nightlies' or the like, with some auto deleting after NNN days ?

    * reference builds (occasional, not every update)
        * cook up an official simulator Docker image and release it through Docker mechanisms
        * cook up an official simulator VirtualBox/Vagrant image and release it through VirtualBox/Vagrant mechanisms
        * add the build plumbing used to create the above to the weewx github trees as well

    * autotest weewx
        * based on some trigger (or manually) run whatever test suite(s) might apply, with a good output

