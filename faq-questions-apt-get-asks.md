### Question asked when upgrading WeeWX

This relatively confusing question is asked by the Debian packaging system:

```
Configuration file '/etc/weewx/weewx.conf'
 ==> Modified (by you or by a script) since installation.
 ==> Package distributor has shipped an updated version.
   What would you like to do about it ?  Your options are:
    Y or I  : install the package maintainer's version
    N or O  : keep your currently-installed version
      D     : show the differences between the versions
      Z     : start a shell to examine the situation
 The default action is to keep your current version.
*** weewx.conf (Y/I/N/O/D/Z) [default=N] ?
```

### If you answer 'N' (the default):

This choice keeps your old configuration file (`weewx.conf`). and is usually the safest choice.

On a typical system, this will simply change the version number near the top of `weewx.conf`,
as well as save versioned copies of your old `weewx.conf` file, and the soon-to-be-new version. 
These are helpful if you later want to see what changed, or if you want to do some 
cut and paste from old to new.

If you make this choice, here is the output to expect:

```
Installing new version of config file /etc/weewx/weewx.conf.dist ...
saving previous config file as /etc/weewx/weewx.conf-4.5.0
saving distribution config file as /etc/weewx/weewx.conf-4.5.1
merging previous and distribution into /etc/weewx/weewx.conf
Using configuration file /etc/weewx/weewx.conf-4.5.0
Processing triggers for systemd (241-7~deb10u7+rpi1) ...
```


### If you answer 'Y' or 'I':

This choice does *not* use your old `weewx.conf`. Instead, it replaces it with a brand new
version. However, it does keep any settings made when you installed WeeWX initially such
as station location and what driver to use. Any other `weewx.conf` edits, such as 
any extensions that you installed, or special reports that you requested, 
will not be transferred over. 

Having said that, `apt-get`, or, more precisely, the utility `dpkg` that `apt-get` uses,
will save your previous copy of `weewx.conf` as `weewx.conf.dpkg-old` which can then
be used to compare to your new `weewx.conf`, allowing you to manually edit it.

This is a good example of why taking the 'N' default is generally the best way to upgrade.

If you make this choice, here is the output to expect:

```

Configuration file '/etc/weewx/weewx.conf'
 ==> Modified (by you or by a script) since installation.
 ==> Package distributor has shipped an updated version.
   What would you like to do about it ?  Your options are:
    Y or I  : install the package maintainer's version
    N or O  : keep your currently-installed version
      D     : show the differences between the versions
      Z     : start a shell to examine the situation
 The default action is to keep your current version.
*** weewx.conf (Y/I/N/O/D/Z) [default=N] ? Y
Installing new version of config file /etc/weewx/weewx.conf ...
Installing new version of config file /etc/weewx/weewx.conf.dist ...
using debconf configuration values from previous install
Processing triggers for systemd (241-7~deb10u7+rpi1) ...
```

