WeeWX works with many different web servers, including Apache, nginx, and lighttpd.

If you install WeeWX from a DEB or RPM package on a system that has a functioning web server, the WeeWX reports should be visible at the path `http://hostname/weewx`

If you cannot see the WeeWX reports, you must tell the web server where to find them.

There are three basic strategies: modify the web server configuration, modify the weewx configuration, or create a symlink.

The following examples show how to make the URL `http://hostname/weewx` point to the `HTML_ROOT` directory `/home/weewx/public_html`

## Strategy 1: Modify the web server configuration

Each web server has a mechanism to alias a URL to a location on disk.  Use this mechanism to tell the web server where to find WeeWX reports.

### Apache 2.2

Create a file `/etc/apache2/conf.d/weewx.conf` with these contents:
~~~~~
Alias /weewx /home/weewx/public_html
<Directory /home/weewx/public_html>
  Options FollowSymlinks
  AllowOverride None
  Order allow,deny
  Allow from all
</Directory>
~~~~~
then restart apache.

### Apache 2.4

Create a file `/etc/apache2/conf.d/weewx.conf` with these contents:
~~~~~
Alias /weewx /home/weewx/public_html
<Directory /home/weewx/public_html>
  Options FollowSymlinks
  AllowOverride None
  Require all granted
</Directory>
~~~~~
then restart apache.

### nginx

Modify the nginx configuration file `/etc/nginx/sites-available` with a new location stanza:
~~~~~
server {
  ...
  location /weewx {
    alias /home/weewx/public_html;
  }
}
~~~~~
then restart nginx.

### lighttpd

~~~~~
alias.url += ( "/weewx" => "/home/weewx/public_html" )
~~~~~

## Strategy 2: Modify `HTML_ROOT` in the WeeWX configuration

The `HTML_ROOT` variable indicates where WeeWX should put its reports.  Specify a different location in the [StdReport] section of the WeeWX configuration file (typically weewx.conf).  For example, this would put the WeeWX reports in `/var/www/html/weewx`, which is in the document root directory `/var/www/html` for many web servers:

~~~~~
[StdReport]
    HTML_ROOT = /var/www/html/weewx
~~~~~

Note that you can specify a different `HTML_ROOT` for each report, if necessary.

## Strategy 3: Create a symlink

With this approach, make a symlink in the web server directory tree to the WeeWX reports.  For example, this would create a symlink for a WeeWX installation installed using setup.py to the `weewx` directory in the standard document root directdory `/var/www/html`:

~~~~~
sudo ln -s /home/weewx/public_html /var/www/html/weewx
~~~~~

# Compatibility changes

There were changes to the location of HTML_ROOT and the web server document root in the weewx DEB package to match the Debian Apache configuration.  The following table indicates where to find WeeWX reports and the web server document tree.

| weeWX | install method | HTML_ROOT |
|---|---|---|
| 3.6+ | setup.py | /home/weewx/public_html |
| 3.6+ | DEB      | /var/www/html/weewx |
| 3.6+ | RPM      | /var/www/html/weewx |
| <= 3.5 | setup.py | /home/weewx/public_html |
| <= 3.5 | DEB      | /var/www/weewx |
| <= 3.5 | RPM      | /var/www/html/weewx |

| Debian | web server | DocumentRoot |
|-----|-----|-----|
| 7 | Apache | /var/www |
| 7 | nginx  | /usr/share/nginx/www |
| 8 | Apache | /var/www/html |
| 8 | nginx  | /var/www/html |
