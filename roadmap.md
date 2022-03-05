# Roadmap

## V4.8
General theme: reduce support burden.

Version 4.8 will support Python 3.5 and later only.
- All Python 2.7 compatibility fixes, including `six`, will be removed. 
- Type hints will be added to functions.

Switch to using `setuptools`, instead of `distutils`.

Include the sdr driver in the standard distribution, and make it autoconfigure.

Do we need the interceptor driver?

Explore using `pip` to install.

Support of a skin that uses SVG graphs, updated dynamically.

Utility `wee_extension` should be able to install directly from a GitHub URL.

Look into [pipx](https://pypi.org/project/pipx/). It seems to be specifically designed to
package Python applications.