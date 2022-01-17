
WeeWX does not install its python libraries into places that the operating system and python typically expect.

Accordingly, you generally need to prepend PYTHONPATH=/some/path/here to your interactive commands that call WeeWX python scripts directly.

### A typical error users run into

Here's a typical error for somebody trying to run the interceptor driver interactively. 


```
$ PYTHONPATH=bin python3 /usr/share/weewx/user/interceptor.py --device=observer \
   --mode=listen --iface=eth0 --filter="src 192.168.0.29 and dst port 8080"

Results in this:
"Traceback (most recent call last):
  File "/usr/share/weewx/user/interceptor.py", line 302, in <module>
    import weewx.drivers
ModuleNotFoundError: No module named 'weewx'"
```

### What is going on here ?

The python built-in search path is not finding the WeeWX libraries because python is not looking in the unexpected custom location of those libraries.

To illustrate, here is how to check python's search path:

```
$ python3 -c "import sys; print('\n'.join(sys.path))"

/usr/lib/python39.zip
/usr/lib/python3.9
/usr/lib/python3.9/lib-dynload
/usr/local/lib/python3.9/dist-packages
/usr/lib/python3/dist-packages
```

See that there is no mention of anything WeeWX related in the list of directories python will search ?

### How to correctly set PYTHONPATH

* for pre-packaged WeeWX - set PYTHONPATH to /usr/share/weewx
* for setup.py installed WeeWX - set PYTHONPATH to /home/weewx/bin

```
# for pre-packaged variants
$ PYTHONPATH=/usr/share/weewx python3 -c "import sys; print('\n'.join(sys.path))"

/usr/share/weewx
/usr/lib/python39.zip
/usr/lib/python3.9
/usr/lib/python3.9/lib-dynload
/usr/local/lib/python3.9/dist-packages
/usr/lib/python3/dist-packages

# for setup.py installations
$ PYTHONPATH=/home/weewx/bin python3 -c "import sys; print('\n'.join(sys.path))"

/usr/lib/python39.zip
/usr/lib/python3.9
/usr/lib/python3.9/lib-dynload
/usr/local/lib/python3.9/dist-packages
/usr/lib/python3/dist-packages
```


### Corrected syntax for the original command above

```
# for pre-packaged WeeWX
PYTHONPATH=/home/weewx/bin python3 /usr/share/weewx/user/interceptor.py --device=observer \
    --mode=listen --iface=eth0 --filter="src 192.168.0.29 and dst port 8080"

# for setup.py installed WeeWX
PYTHONPATH=/usr/share/weewx python3 /usr/share/weewx/user/interceptor.py --device=observer \
   --mode=listen --iface=eth0 --filter="src 192.168.0.29 and dst port 8080"
```
