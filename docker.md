# Running WeeWX in Docker

WeeWX can run well in Docker, especially if your weather station is network-accessible.  This can simplify the installation process.

You can start with the [Docker image for weeWX](https://github.com/jgoerzen/docker-weewx) -- there are instructions there on how to easily use it.  The image will cover the installation, and then all you need to handle is the configuration after that.

If you are planning on using the interceptor driver, you may get a port conflict, so I provide an image with a custom macvlan driver, avoiding it.
This is a Docker implementation of weewx, in interceptor mode, with neowx skin based on debian:stretch (https://github.com/MrNonoss/weewx)