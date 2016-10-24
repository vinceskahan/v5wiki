WeeWX will work with many different web servers, including Apache, nginx, and lighttpd.

If you install WeeWX from DEB or RPM package on a recent operating system, everything should just work.  By default, WeeWX puts its reports in the web server's document tree.  However, if you install using setup.py, or if you use an older operating system, you might have to do some configuration.

There are a few ways to let the web server know where to find the WeeWX reports.

1. Web server configuration.  Modify the web server configuration file(s) to include the WeeWX reports.  Here are examples for various web servers:

## Apache 2.2

~~~~
Alias /weewx /home/weewx/public_html
<Directory /home/weewx/public_html>
  Options FollowSymlinks
  AllowOverride None
  Order allow,deny
  Allow from all
</Directory>
~~~~

## Apache 2.4

~~~~
Alias /weewx /home/weewx/public_html
<Directory /home/weewx/public_html>
  Options FollowSymlinks
  AllowOverride None
  Require all granted
</Directory>
~~~~

## nginx

~~~~
server {
  location /weewx {
    alias /home/weewx/public_html;
  }
}
~~~~

## lighttpd

~~~~
alias.url += ( "/weewx" => "/home/weewx/public_html/" )
~~~~

2. WeeWX configuration using HTML_ROOT.  Set HTML_ROOT to a path in the web server's document tree.  For example:

~~~~
[StdReport]
    HTML_ROOT = /var/www/html
~~~~

3. Symlink.  Link the WeeWX report directory to a location in the web server's document tree.  For example, if `HTML_ROOT` is `/home/weewx/public_html` and the web server's document root is `/var/www/html`, do something like this:

~~~~
sudo ln -s /home/weewx/public_html /var/www/html/weewx
~~~~

## Default locations

The location of HTML_ROOT is different in various WeeWX installation configurations.  To 

HTML_ROOT
WeeWX 3.6+
DEB /var/www/html
RPM /var/www/html
setup.py /home/weewx/public_html

WeeWX 3.5-
DEB /var/www
RPM /var/www/html
setup.py /home/weewx/public_html

DocumentRoot
Debian 7
Apache /var/www
nginx /usr/share/nginx/www
Debian 8
Apache /var/www/html
nginx /var/www/html
Redhat
Apache /var/www/html
