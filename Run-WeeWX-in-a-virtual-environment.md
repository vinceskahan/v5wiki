It's a little tricky to setup WeeWX from the virtual environment by simply running `setup.py` because things end up in funny places.

Instead, use the system's python to set things up using `setup.py`, then switch to the virtual environment to install dependencies, then run `weewxd`. But, first you'll need `configobj`, because `setup.py` requires it. However, DO NOT INSTALL `configobj` from the weewx distribution subdirectory, because `pip` will see the `setup.cfg` and put `configobj` in a funny spot.

Here's  the way I do it:

```shell
# First install configobj. Can be done anywhere, but NOT in the weewx distribution subdirectory
cd /home/weewx
python3 -m pip install --user configobj
# Unpack the tarball, then cd into the resultant subdirectory
tar xvf weewx-4.9.1.tar.gz
cd weewx-4.9.1
# Set up /home/weewx using the system's version of python
python3 setup.py install
# Now set up the venv in /home/weewx
cd /home/weewx
python3 -m venv weewx_venv
# Activate it
source ./weewx_venv/bin/activate
# Install dependencies in the  virtual environment
python3 -m pip install configobj Cheetah3 Pillow pyserial pyusb pyephem
# Now you can run weewxd in the virtual environment
python3 ./bin/weewxd
```

For running as a systemd daemon, set up your systemd file so that it uses the version of Python in the venv. That is, `ExecStart` will look something like this:

```
ExecStart=/home/weewx/weewx_venv/bin/python3 /home/weewx/bin/weewxd /home/weewx/weewx.conf
```

A major goal of WeeWX Version 5 is to make this all easier. In particular, use `pipenv`, or similar tool, to install things from [pypi.org](http://pypi.org/) into a virtual environment. Unfortunately, we may end up with a bit of a hybrid mix because the Python install tools are not really designed to handle data such as the skins and configuration file.
