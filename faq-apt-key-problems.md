## apt key installation issues

In late November 2021, LetsEncrypt changed their digital certificate setup which is used for securely connecting to websites (such as weewx.com) and for gpg signature validation of digitally signed files (such as the apt command to validate packages).  This upstream change potentially impacts whether you will see an error in installing the apt key for the weewx package repos, as per the [debian installation instructions](https://weewx.com/docs/debian.htm).

On a sufficiently patched system, step-one of the debian instructions should return quietly with no errors and the subsequent steps should work as written.

If you have no errors and steps-two and beyond worked, stop reading here...


### what this error looks like:

A typical error caused by this upstream breaking change would look like:
```
# wget -qO - https://weewx.com/keys.html | sudo gpg --dearmor --output /etc/apt/trusted.gpg.d/weewx.gpg
gpg: no valid OpenPGP data found.
```

The actual problem here is the `wget` command failed to believe that weewx.com was the 'real' weewx.com, likely because your os is not sufficiently updated to trust the upstream LetsEncrypt digital certificate used by the weewx.com website you are accessing.


### To fix the problem:

You have two options:

1. (preferred) update your system to current via the usual method
```
# preferred - tested via upgrade from 10.4 to 10.11 debian_version on a Raspberry Pi
sudo apt-get update
sudo apt-get upgrade -y
```

2. (workaround) or add the `--no-check-certificate` to the `wget` command in the debian instructions.  This will tell wget to trust the contents of the website without validating that the site is secure.   Use your judgement regarding whether that is an acceptable risk, but remember that the files you are downloading are the 'public' keys used to digitally sign the weewx packages, and that apt will not install them if the signatures don't match, so this might be acceptable to you.

```
# workaround
wget --no-check-certificate -qO - https://weewx.com/keys.html | sudo gpg --dearmor --output /etc/apt/trusted.gpg.d/weewx.gpg
```

### how to check your os version

* You can check your os debian version by `cat /etc/debian_version`
* Your dpkg.log file(s) are in `/var/log`

### how to check your apt keys

Look for the weewx keys in the `sudo apt-key list` output:

```
$ sudo apt-key list
/etc/apt/trusted.gpg
--------------------
pub   rsa2048 2012-04-01 [SC]
      A0DA 38D0 D76E 8B5D 6388  7281 9165 938D 90FD DD2E
uid           [ unknown] Mike Thompson (Raspberry Pi Debian armhf ARMv6+VFP) <mpthompson@gmail.com>
sub   rsa2048 2012-04-01 [E]

pub   rsa2048 2012-06-17 [SC]
      CF8A 1AF5 02A2 AA2D 763B  AE7E 82B1 2992 7FA3 303E
uid           [ unknown] Raspberry Pi Archive Signing Key
sub   rsa2048 2012-06-17 [E]

pub   rsa4096 2014-06-13 [SC]
      9FD3 B784 BC1C 6FC3 1A8A  0A1C 1655 A0AB 6857 6280
uid           [ unknown] NodeSource <gpg@nodesource.com>
sub   rsa4096 2014-06-13 [E]

/etc/apt/trusted.gpg.d/weewx.gpg
--------------------------------
pub   rsa2048 2014-11-15 [SC]
      EAC2 0711 8C0B D050 B835  9313 A0CB 255B 75BF 977C
uid           [ unknown] Thomas Keffer (Author of weewx) <tkeffer@gmail.com>
sub   rsa2048 2014-11-15 [E]

pub   dsa1024 2014-01-28 [SC]
      1E9F F20B 86EF 78AB 3B45  4944 ED44 4FCC F0E2 B09E
uid           [ unknown] Matthew Wall (weewx) <mwall@users.sourceforge.net>
sub   elg1024 2014-01-28 [E]
```

If you've previously added the keys via legacy methods, you might see the weewx keys installed into the one-big trusted.gpg file.  This is ok.
