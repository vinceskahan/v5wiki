This Wiki entry is for troubleshooting Pillow.

## ImportError: `libopenjp2.so.7`

If you get this error (check the log!)

```
...
Oct  8 00:05:23 rpi2 weewx[1295] ERROR weewx.reportengine: Unable to instantiate generator 'weewx.imagegenerator.ImageGenerator'
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****  libopenjp2.so.7: cannot open shared object file: No such file or directory
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****  Traceback (most recent call last):
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****    File "/home/tkeffer/weewx-venv/lib/python3.9/site-packages/weewx/reportengine.py", line 177, in run
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****      obj = weeutil.weeutil.get_object(generator)(
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****    File "/home/tkeffer/weewx-venv/lib/python3.9/site-packages/weeutil/weeutil.py", line 1352, in get_object
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****      mod = __import__(module)
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****    File "/home/tkeffer/weewx-venv/lib/python3.9/site-packages/weewx/imagegenerator.py", line 14, in <module>
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****      import weeplot.genplot
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****    File "/home/tkeffer/weewx-venv/lib/python3.9/site-packages/weeplot/genplot.py", line 13, in <module>
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****      from PIL import Image, ImageDraw, ImageFont
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****    File "/home/tkeffer/weewx-venv/lib/python3.9/site-packages/PIL/Image.py", line 82, in <module>
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****      from . import _imaging as core
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****  ImportError: libopenjp2.so.7: cannot open shared object file: No such file or directory
Oct  8 00:05:24 rpi2 weewx[1295] ERROR weewx.reportengine:         ****  Generator ignored
...
```

your operating system is missing the OpenJPEG library. This can happen if a
Python wheel is not available for Pillow on your operating system &mdash;
very common when working on 32-bit systems, such as the Raspberry Pi Zero. 

The fix is easy: just install it:

```{ .shell .copy }
sudo apt install libopenjp2-7
```

