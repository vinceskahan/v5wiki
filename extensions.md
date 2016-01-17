### Extensions to weewx

There are many ways to customize weewx.  Customizations typically fall into one of these categories:

*   search list extension
*   template
*   skin
*   service
*   generator
*   driver

Extensions are a way to package one or more customizations so they can be installed and distributed as a functional group.

Each extension is typically distributed as a archive that contains the files used by the extension.  The archive might be a tar archive (.tar), a compressed archive (.tgz), or a zip archive (.zip).  An extension can also be installed from an expanded archive, such as those in the extensions directory of the weewx distribution.

To manage extensions, use the wee_extension tool that comes with weewx.  The location of wee_extension depends on how weewx was installed.

When weewx is installed using setup.py and the default location of /home/weewx, wee_extension is installed to /home/weewx/bin/wee_extension

When weewx is installed using .deb or .rpm, wee_extension is installed to /usr/share/weewx/wee_extension with a symbolic link /usr/bin/wee_extension

### How to install an extension

The general form looks like this:

```
wee_extension --install filename.tgz
```

Let's say that the extension weewx-cmon-0.3.tgz has been saved to the Downloads folder in the user's home directory.  (When downloading an extension, be sure that your web browser does not uncompress or expand the contents of the tarball.)

```
/home/username/Downloads/weewx-cmon-0.3.tgz
```

For a setup.py installation, the command becomes:

```
/home/weewx/bin/wee_extension --install ~/Downloads/weewx-cmon-0.3.tgz
```

For a .deb or .rpm installation, the command is simply:

```
wee_extension --install ~/Downloads/weewx-cmon-0.3.tgz
```

### How to uninstall an extension

To uninstall an extension, specify the unadorned name of the extension.

The general form looks like this:

```
wee_extension --uninstall extension_name
```

In the previous example, the extension tarball weewx-cmon-0.3.tgz contained the 'cmon' extension.

For a setup.py installation, the command becomes:

```
/home/weewx/bin/wee_extension --uninstall cmon
```

For a .deb or .rpm installation, the command is simply:

```
wee_extension --uninstall cmon
```

### What extensions are installed?

The general form looks like this:

```
wee_extension --list
```

For a setup.py installation, the command becomes:

```
/home/weewx/bin/wee_extension --list
```

For a .deb or .rpm installation, the command is simply:

```
wee_extension --list
```


This will list only extensions that have been installed using the extension manager.  Customizations that were applied manually will not be listed.