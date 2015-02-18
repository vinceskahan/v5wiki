## Extensions to weewx

There are many ways to customize weewx.  Customizations typically fall into one of these categories:

*   search list extension
*   template
*   skin
*   service
*   generator
*   driver

Extensions are a way to package one or more customizations so they can be installed and distributed as a functional group.

Each extensions is distributed as a tarball - a compressed archive (.tgz) that contains the files used by the extension.

To manage extensions, use the setup.py tool that comes with weewx.  When you install weewx, setup.py is installed with the other weewx components.  The location of setup.py depends on how weewx was installed.

When weewx is installed using setup.py and the default location of /home/weewx, setup.py is installed to /home/weewx/setup.py

When weewx is installed using .deb or .rpm, setup.py is installed to /usr/share/weewx/setup.py with a symbolic link /usr/bin/wee_setup

### How to install an extension

The general form looks like this:

`setup.py install --extension filename.tgz`

Let's say that the extension weewx-cmon-0.3.tgz has been saved to the Downloads folder in the user's home directory.  (When downloading an extension, be sure that your web browser does not uncompress or expand the contents of the tarball.)

`/home/username/Downloads/weewx-cmon-0.3.tgz`

For a setup.py installation, the command becomes:

`/home/weewx/setup.py install --extension ~/Downloads/weewx-cmon-0.3.tgz`

For a .deb or .rpm installation, the command becomes:

`/usr/share/weewx/setup.py install --extension ~/Downloads/weewx-cmon-0.3.tgz`

or

`wee_setup install --extension ~/Downloads/weewx-cmon-0.3.tgz`

### How to uninstall an extension

To uninstall an extension, specify the unadorned name of the extension.

The general form looks like this:

`setup.py uninstall --extension extension_name`

In the previous example, the extension tarball weewx-cmon-0.3.tgz contained the 'cmon' extension.

For a setup.py installation, the command becomes:

`/home/weewx/setup.py  uninstall --extension cmon`

For a .deb or .rpm installation, the command becomes:

`/usr/share/weewx/setup.py  uninstall --extension cmon`

or

`wee_setup  uninstall --extension cmon`

### What extensions are installed?

The general form looks like this:

setup.py list-extensions

For a setup.py installation, the command becomes:

`/home/weewx/setup.py list-extensions`

For a .deb or .rpm installation, the command becomes:

`/usr/share/weewx/setup.py list-extensions`

or

`wee_setup list-extensions`

This will list only extensions that have been installed using the extension manager.  Customizations that were applied manually will not be listed.