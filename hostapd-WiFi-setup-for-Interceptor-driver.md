## Note... This works for any debian based system
* Debian
* Ubuntu
* Kubuntu
* Raspberry Pi (Raspbian) or other Raspberry Pi debian OS

# Prerequisites
[weewx-interceptor](https://github.com/matthewwall/weewx-interceptor)

* 1) download the interceptor driver

`wget -O weewx-interceptor.zip https://github.com/matthewwall/weewx-interceptor/archive/master.zip`

* 2) install the driver

`sudo weectl extension install weewx-interceptor-master.zip`

<pre>The 'sniff' mode requires installation of the pypcap or pylibpcap module, each
of which in turn depends on libpcap.  Install the pcap dependencies using pip:

  sudo pip install pypcap

or using apt-get on debian systems:

  sudo apt-get install python-libpcap
</pre>

**NOTE !! DO NOT INSTALL BIND9 !!**<br>
If you do have it installed remove it **FIRST**

**systemctl stop bind9<br>
  apt remove bind9**
***
<pre>install hostapd
install dnsmasq

systemctl stop dnsmasq
systemctl stop hostapd</pre>

Edit /etc/dhcpcd.conf<br>
and edit the first 4 lines below for your<br>
NETWORK ROUTER static IP
-------------------------------------------
<pre>
interface eth0
static ip_address=192.168.0.4/255.255.255.0
static routers=192.168.0.1
static domain_name_server=8.8.8.8 8.8.4.4

# Defaults from Raspberry Pi configuration
hostname
clientid
persistent
option rapid_commit
option domain_name_servers, domain_name, domain_search, host_name<br>
option classless_static_routes
option ntp_servers
require dhcp_server_identifier
slaac private
nohook lookup-hostname

# RaspAP-WebGui wireless configuration
interface wlan0
static ip_address=10.3.141.1/24
static routers=10.3.141.1
static domain_name_servers=1.1.1.1 8.8.8.8
</pre>
-------------------------------------------

<pre>mv /etc/dnsmasq.conf /etc/dnsmasq.conf.orig</pre>

Edit /etc/dnsmasq.conf
-------------------------------------------
<pre>domain-needed
interface=wlan0
dhcp-range=10.3.141.50,10.3.141.255,255.255.255.0,12h</pre>
-------------------------------------------

### Edit /etc/hostapd/hostapd.conf<br>
1. Edit for your AP _ssid_ name **ssid=YOUR-AP-NAME**
1. Make sure you edit _wpa_passphrase_ **wpa_passphrase=WIFI-PASSWORD**
-------------------------------------------
<pre>interface=wlan0
driver=nl80211
ssid=raspi-webgui
hw_mode=g
channel=1
wmm_enabled=0
macaddr_acl=0
auth_algs=1
ignore_broadcast_ssid=0
wpa=2
wpa_passphrase=WIFI-PASSWORD
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP
rsn_pairwise=CCMP</pre>
--------------------------------------------

Edit /etc/default/hostapd
--------------------------------------------
<pre>
# Defaults for hostapd initscript
#
# See /usr/share/doc/hostapd/README.Debian for information about alternative
# methods of managing hostapd.
#
# Uncomment and set DAEMON_CONF to the absolute path of a hostapd configuration
# file and hostapd will be started during system boot. An example configuration
# file can be found at /usr/share/doc/hostapd/examples/hostapd.conf.gz
#
#DAEMON_CONF=""
DAEMON_CONF="/etc/hostapd/hostapd.conf"

# Additional daemon options to be appended to hostapd command:
#       -d   show more debug messages (-dd for even more)
#       -K   include key data in debug messages
#       -t   include timestamps in some debug messages
#
# Note that -B (daemon mode) and -P (pidfile) options are automatically
# configured by the init.d script and must not be added to DAEMON_OPTS.
#
#DAEMON_OPTS="/var/log/hostapd.log"
</pre>
--------------------------------------------

Edit /etc/wpa_supplicant/wpa_supplicant.conf
--------------------------------------------
<pre>ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US</pre>
--------------------------------------------

Edit /etc/sysctl.conf and add line
--------------------------------------------
<pre>net.ipv4.ip_forward=1</pre>
--------------------------------------------

Run the following as root
--------------------------------------------
<pre>iptables -t nat -A  POSTROUTING -o eth0 -j MASQUERADE
iptables-save > /etc/iptables.ipv4.nat</pre>
--------------------------------------------

Edit /etc/rc.local and add BEFORE exit 0
--------------------------------------------
<pre>iptables-restore < /etc/iptables.ipv4.nat</pre>
--------------------------------------------

Reboot your Raspberry Pi

End hostapd setup
--------------------------------------------

## weeWX setup
--------------------------------------------
Edit weewx.conf
<pre>
    # Set to type of station hardware. There must be a corresponding stanza
    # in this file with a 'driver' parameter indicating the driver to be used.
    station_type = Interceptor

[Interceptor]

  driver = user.interceptor
  device_type = observer
  mode = sniff
  iface = wlan0
  pcap_filter = src 10.3.141.62 and dst port 80</pre>

Modified from https://www.raspberrypi.org/documentation/configuration/wireless/access-point.md

_Original Author: Scott Grayban_
