# Weewx Driver Development Tips

## Starting Point
When building a new driver start at [The Customization Guide](https://www.weewx.com/docs/customizing.htm#porting).  You can familiarize yourself with the architecture and basic requirements there.  You can use the [fileparse driver](https://github.com/weewx/weewx/blob/master/examples/fileparse/bin/user/fileparse.py) in the examples directory of the source code as a good starting point.

## Directory Structure
Custom drivers/extensions should not be installed in the weewx/drivers directory.  Instead they ideally should be installed as extensions that are installed using wee_extension.  To accomplish this setup your source files in the following fashion.

    bin/user/mydriver.py - This is the driver file itself
    install.py - This is the extension installer, see the next section for details.
    README.md - Not mandatory but super helpful for your users.  You should document your module, options and config here.
    LICENSE - Not mandatory but recommended.  The open source license of your choice goes here.

The `bin/user` directory will help ensure you're driver is installed in the correct location.  For example, on Ubuntu this would be `/usr/share/weewx/share`.  

## install.py
The install.py file implements a loader and a class that inherits from [ExtensionInstaller](https://github.com/weewx/weewx/blob/master/bin/weecfg/extension.py).  A basic skeleton for your file might look like the one below.

    from setup import ExtensionInstaller

    def loader():
        return YOURDRIVERInstaller()

    class YOURDRIVERInstaller(ExtensionInstaller):
        def __init__(self):
            super(YOURDRIVERInstaller, self).__init__(
                version=".1",
                name='YOURDRIVER',
                description='YOUR DRIVER installer',
                author="YOUR NAME",
                author_email="YOUR@EMAIL.ADDY",
                files=[('bin/user', ['bin/user/YOURDRIVER.py'])],
                config={
                    'YOURDRIVER': {
                        'driver' : 'bin.user.YOURDRIVER,
                        'config_param_1': 'YOUR_VALUE_1',
                        'config_param_2': 'YOUR_VALUE_2',
                        'config_param_3': 'YOUR_VALUE_3'
                    },
                }
            )

As you can see above the class sets some basic metadata about your driver.  Pay special attention to the files and config values.
* The files tuple identifies what files are needed in what directory.  More than one file is supported in the file list but usually you'll have a single file.
* The config dictionary sets up your configuration parameters.  At a minimum you'll want the stanza to be named with your driver's name and the driver parameter should be set to `bin.user.YOURDRIVER` with YOURDRIVER being your driver's name.  This will be used both by wee_install and wee_extension.

## Installation
To install your driver you can either download a zip (most easily done using the github download as zip button), tgz or a directory containing your driver files.  I.e. a clone of your repo if you're using github.  To install your driver use the `wee_extension` command like so.

    wee_extension --install YOURDRIVER.zip

If all goes well your driver will be installed and your config stanza be added to the weewx.conf.  ***Note that it will appear at the bottom of the file!***

At this point you should set any custom configuration values and then run `wee_config` to start using your driver.  After editing you'll want to run the command below.

    wee_config --reconfigure --driver=YOURDRIVER --no-prompt

The `--no-prompt` flag tells `wee_config` not to ask you about all the key setup settings like latitude/longitude etc...  After this you should be able to inspect your weewx.conf, see that the station_type has been set to your driver and the driver configuration stanza will be right below the `[Station]` stanza for handy tweaking and debugging.

_You may need to run these commands using sudo on your system!_

More information about the weewx utilities like wee_config and wee_extension can be found in the [Utilities Guide](https://weewx.com/docs/utilities.htm).

## Errata
* Setting default values at installation time, rather than editing the weewx.conf file, is left as an exercise for the reader.  It seems like it should be possible via the install.py file.  If anyone finds an implementation like this I recommend they document how it works in this wiki page.
* When installing the extension the driver's stanza is unceremoniously dumped to the end of the weewx.conf file.  The `wee_config` utility knows how to re-order them correctly but that doesn't seem to yet be part of the `wee_extension` code.  This can be confusing if you're assuming there's a way to reset station_type and place the driver stanza in the "normal" location prior to running `wee_config`.
* It's possible to preserve comments and create a cleaner doc string version of your config stanza.  To do so you need to put the raw stanza in a doc string and then leverage the [ConfigObject](https://pythonhosted.org/ConfigObject/) library to parse it and hand it to your driver's extension class.  An example for this would be lovely in this page but I've yet to accomplish it so it remains a todo at the time of this writing.
