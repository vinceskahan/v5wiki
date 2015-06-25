This article will describe how to troubleshoot issues with your python imaging library.  The preferred imaging library is Pillow.  Pillow is a modern version of PIL.

## Pre-requisite libraries

You must make sure you have zlib-dev, libfreetype-dev and jpeg-dev installed on your system 

**debian:**

`sudo apt-get install libjpeg-dev zlib1g-dev libfreetype-dev`

after you have installed these, you will have to reinstall pillow so that it incorporates those libraries

`pip uninstall pillow`

`pip install pillow`

Need pip? (first uninstall pillow however you got it first)

**debain:** sudo apt-get install python-pip

[others](https://pip.pypa.io/en/latest/installing.html) 

## Troubleshooting

Open python

`python`

You should see >>>
### zlib test:

`>>> import zlib`

`>>> a=zlib.compress('hello world')`

`>>> print zlib.decompress(a)`

`hello world`

### Test image png/jpg creation

`>>> from PIL import Image`

`>>> i = Image.open('/home/weewx/public_html/smartphone/icons/icon_ipad_x1.png')`

`>>> i.save('b.png')`

`>>>`

should return you to the python prompt

`IOError: decoder zip not available`

If you get the above error, it's likely you need to install or reinstall zlib-dev

You must have a jpg and png to see if it works (note the path that I used from weewx to test the png)

Finally if you get the error: `The _imagingft C module is not installed` 

You need to re-install libfreetype

Also useful is to check image core to see if the libraries are loaded.  Use this to compare to your list.

`>>> from PIL import Image`

`>>> dir(Image.core)`

`['DEFAULT_STRATEGY', 'FILTERED', 'FIXED', 'HUFFMAN_ONLY', 'PILLOW_VERSION', 'RLE', '__doc__', '__file__', '__name__', '__package__', 'alpha_composite', 'bit_decoder', 'blend', 'convert', 'copy', 'crc32', 'draw', 'effect_mandelbrot', 'effect_noise', 'eps_encoder', 'fill', 'fli_decoder', 'font', 'getcodecstatus', 'getcount', 'gif_decoder', 'gif_encoder', 'hex_decoder', 'hex_encoder', 'jpeg_decoder', 'jpeg_encoder', 'jpeglib_version', 'linear_gradient', 'map_buffer', 'msp_decoder', 'new', 'open_ppm', 'outline', 'packbits_decoder', 'path', 'pcd_decoder', 'pcx_decoder', 'pcx_encoder', 'radial_gradient', 'raw_decoder', 'raw_encoder', 'sun_rle_decoder', 'tga_rle_decoder', 'tiff_lzw_decoder', 'wedge', 'xbm_decoder', 'xbm_encoder', 'zip_decoder', 'zip_encoder', 'zlib_version']`

Ctrl-D to exit python