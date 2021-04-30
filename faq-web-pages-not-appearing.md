## Why are my web pages not appearing ?

There are a few typical scenarios that cause this to happen.

### User impatience

Web pages and images are generated after the first report interval, which is generally 5 minutes.  Wait until the first report interval passes and check your syslog to see that the reports have been run.  A typical system log will look something like:

```
Apr 30 10:51:37 pi4p2 weewx[4523] INFO weewx.engine: Starting main packet loop.
Apr 30 10:55:14 pi4p2 weewx[4523] INFO weewx.manager: Added record 2021-04-30 10:55:00 AKDT (1619808900) to database 'weewx.sdb'
Apr 30 10:55:14 pi4p2 weewx[4523] INFO weewx.manager: Added record 2021-04-30 10:55:00 AKDT (1619808900) to daily summary in 'weewx.sdb'
Apr 30 10:55:16 pi4p2 weewx[4523] INFO weewx.cheetahgenerator: Generated 8 files for report SeasonsReport in 1.77 seconds
Apr 30 10:55:17 pi4p2 weewx[4523] INFO weewx.imagegenerator: Generated 15 images for report SeasonsReport in 0.52 seconds
Apr 30 10:55:17 pi4p2 weewx[4523] INFO weewx.reportengine: Copied 5 files to /var/www/html/weewx
```




### Incorrect URL

WeeWX's default location for the public_html tree is a subdirectory 'weewx' under the top of your webserver's 'docroot' directory.

The fix is to open "http://myhostname.com/weewx" to see the WeeWX web.


### Mismatch in WeeWX and webserver configurations

For setup.py installations specifically, WeeWX will write to /home/weewx/public_html by default, yet the default webservers generally expect the web documentation root to be in is /var/www/html, which is a different location.   So if you look at the WeeWX configuration it looks fine, as do the syslogs, yet if you try to open the web via a browser you get a file-not-found error.

The fix is to make both pieces of software align in where on the filesystem WeeWX will put its generated web pages and images.   Instructions are elsewhere on the wiki [(link)](webserver)

### No webserver is installed

Most operating systems do 'not' install a webserver in the default minimal-bootable os image.   In particular, Raspberry pi users running the Lite version of the Raspberry Pi OS (aka Raspbian or RaspiOS)run into this problem.

When you install WeeWX via the pre-packaged apt-get procedure, the installer will recommend what you should add to your system:

```
pi@pi4p2:~ $ sudo apt-get install weewx
Reading package lists... Done
Building dependency tree
Reading state information... Done
Suggested packages:
  sqlite ftp httpd
```

On a Raspberry Pi, if you try to install 'httpd' you will get the following set of choices that match available web servers you may choose from.  You need to pick which one of the many choices you would like to install.  Most people choose 'nginx' or 'apache2' or 'lighthttpd'.  There is more information on this elsewhere in the [wiki](webserver).


```
pi@pi4p2:~ $ sudo apt-get install httpd
Reading package lists... Done
Building dependency tree
Reading state information... Done
Package httpd is a virtual package provided by:
  yaws 2.0.6+dfsg-1+deb10u1
  webfs 1.21+ds1-12
  tntnet 2.2.1-3
  nginx-light 1.14.2-2+deb10u3
  nginx-full 1.14.2-2+deb10u3
  nginx-extras 1.14.2-2+deb10u3
  monkey 0.9.3-1
  mini-httpd 1.30-0.2
  micro-httpd 20051212-15.1
  mathopd 1.5p6-1.1
  lighttpd 1.4.53-4+deb10u1
  ebhttpd 1:1.0.dfsg.1-4.3
  bozohttpd 20111118-1+b1
  boa 0.94.14rc21-5
  apache2 2.4.38-3+deb10u4
  aolserver4-daemon 4.5.1-18.1
  aolserver4-core 4.5.1-18.1
You should explicitly select one to install.

E: Package 'httpd' has no installation candidate
```
