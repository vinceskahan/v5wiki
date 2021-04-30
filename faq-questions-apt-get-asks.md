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

On a typical minimal system, this will simply change the courtesy version variable near the top of weewx.conf, but it will also save weewx.conf.<version> files for both your old version and the to-be new version.  These are helpful if you want to later compare things, or do some cut+paste from old to new if anything was missed by the installer in the upgrade process.

In most cases you want to take the installer defaults.

```
Installing new version of config file /etc/weewx/weewx.conf.dist ...
saving previous config file as /etc/weewx/weewx.conf-4.5.0
saving distribution config file as /etc/weewx/weewx.conf-4.5.1
merging previous and distribution into /etc/weewx/weewx.conf
Using configuration file /etc/weewx/weewx.conf-4.5.0
Processing triggers for systemd (241-7~deb10u7+rpi1) ...
```


### If you answer 'Y' or 'I':

This does not save your previous weewx.conf, but it does keep any settings made when you installed weewx initially by answering questions 'debconf' asked you. If you had previously added skins or extensions into /home/weewx/bin/user, those are kept, but any accompanying weewx.conf edits made by the extension installer (or manual edits) are deleted from the weewx.conf file.

Fortunately 'dpkg' under the hood saves your previous weewx.conf as 'weewx.conf.dpkg-old' which can be used to compare to your new weewx.conf so that you can manually replace the edits made.

(this is a good example of why taking the 'N' default is generally the best way to upgrade)

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

