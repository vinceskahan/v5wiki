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
* doing an install of each supported package on a virgin system
* doing an upgrade of each supported package to a system with the previous weeWX version already installed

## How can we improve the process?

* 