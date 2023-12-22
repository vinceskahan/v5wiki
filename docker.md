# Running WeeWX in Docker

WeeWX can run well in Docker, especially if your weather station is network-accessible.  This can simplify the installation process.

You can start with the [Docker image for weeWX](https://github.com/jgoerzen/docker-weewx) -- there are instructions there on how to easily use it.  The image will cover the installation, and then all you need to handle is the configuration after that.

If you are planning on using the interceptor driver, you may get a port conflict, so I provide an image with a custom macvlan driver, avoiding it.
This is a Docker implementation of weewx, in interceptor mode, with neowx skin based on debian:stretch (https://github.com/MrNonoss/weewx)

### Logging

In order to run WeeWX in docker, you must configure it to use standalone logging, since there is no system logging facility within docker.  Since WeeWX V4 this is pretty easy - just add a `[Logging]` stanza to the WeeWX configuration file, then WeeWX will log to a file, and the file will be rotated regularly.

See the section *Make WeeWX skip the system logging facility* in the wiki article about [*Configuring logging*](logging).
