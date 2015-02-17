# Notes on porting <span class="code">weewx</span> to the SheevaPlug

The SheevaPlug is a very small, very low-powered general purpose computer. Equipped with a single USB port and a single Ethernet port and a modest amount of memory (512 MB) and storage (512 MB), in many ways it is an ideal server for a weather station. The website [openplug.org](http://www.openplug.org) has a good &quot;[Quick Start](http://www.plugcomputer.org/Documentation/quickstart/)&quot; guide on how to get up and running on the Plug.

Porting weewx to it is very straightforward. For the most part, follow the directions
[New Plugger How To](http://www.plugcomputer.org/Documentation/howtos/new-plugger-how-to/) in the OpenPlug wiki on how to set up the Plug.

When you get to the &quot;Pause for a second&quot; section, you have a choice. You can either continue using the kernel and version of Ubuntu that comes with the Plug &quot;as is&quot;, or you can install a newer version.

My SheevaPlug came with kernel 2.6.22.18, which does not support the CP2101 USB serial device used by the VantagePro, necessitating a kernel upgrade. However, if you have a serial interface to your VantagePro and you are planning on using a USB-to-serial converter, you might well be in luck if your converter uses something that is recognized by the 2.6.22.18 kernel. I think it&#39;s worth trying the out-of-the-box SheevaPlug before messing around with upgrading the kernel --- you might get lucky. I wasn&#39;t. If it doesn&#39;t work out, you&#39;ll only lose about 30 minutes of work.

# 1. Installing V1.0 Installer

This section assumes that you will need to upgrade your kernel. If you don&#39;t, or if you want to give the existing kernel a try, skip it and go on to the next section, [Installing weewx](http://www.weewx.com/docs/sheeva.htm#Installing_weewx). If things don&#39;t work out, you can always come back here.

If you need to upgrade your kernel, the easiest way to do so is to use the new V1.0 Installer. Unfortunately, the readme file that comes with the installer is very confusing. [ Much better instructions can be found at this link.](http://www.plugcomputer.org/Documentation/howtos/sheevaplug-installer/)

One of the first things you will need to do is establish connectivity to the SheevaPlug by using its serial port (instead of via SSH through its Ethernet port). Here are directions for both [Linux](http://www.plugcomputer.org/Documentation/howtos/serial-terminallinuxprograms/) or [Windows](http://www.plugcomputer.org/Documentation/howtos/serialterminalwindowsputty/). I was able to get both to work. As I understand it, the install process is managed and controlled through this port, although the actual files come off of a USB stick.

As per the instructions, download the tarball and extract.

Move the following files from subdirectory <span class="code">sheevaplug-installer-v1.0/installer</span> 
to to the root directory of a FAT 32 USB stick:
~~~~
initrd
modules.tar.gz
README.txt
rootfs.tar.gz
ubuntu-sheevaplug.sh
uImage
~~~~
Plug the stick into the USB port of the SheevaPlug.

I then tried installing the new kernel using a Windows host but had absolutely no luck. After running &quot;<span class="code">runme.exe nand</span>&quot; I would keep gettting the error:
~~~~
Error: unable to open ftdi device: device not found
Runtime error, file &quot;command.c&quot;, line 469:
&nbsp;****&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; openocd FAILED
&nbsp;****&nbsp;&nbsp; Is the mini USB cable connected?
&nbsp;****&nbsp;&nbsp; Try powering down, then replugging the Sheevaplug
~~~~
I tried several different ways, including rebooting with out running putty on the serial port. I also tried, as per [ these directions](http://www.openplug.org/plugwiki/index.php/SheevaPlug_Installer#Important_Note_for_Newer_Plugs_.287.2F30.2F09ish.29), editing the file <span class="code"> sheevaplug-installer-v1.0/uboot/openocd/config/interfaces/sheevaplug.cfg</span>. No luck (in fact, when I finally got things working using a Linux host, it was the original settings that did the trick).

Here&#39;s how to install from a Linux host. From a Ubuntu 9.10 host I installed php:
~~~~
apt-get install php5-cli
~~~~
Then ran the Linux script

php runme.php nand

Got an error: libftdi.so.1 no such file. I installed it:
~~~~
apt-get install libftdi1
~~~~
Still no luck. Then I unplugged then plugged in USB cable. Tried again. 
Worked!

This put me at kernel 2.6.30.2.

# <a name="Installing_weewx">2. Installing weewx</a>

Now it&#39;s just a simple matter of installing weewx, albeit with a few more missing pieces. I did the following installs/configurations to the operating system. You may want to do more, or leave some out. When I was done I had about 256 MB of disk space left on the internal flash drive (out of the original 512 MB).
~~~~
apt-get update
apt-get dist-upgrade (big one)
apt-get autoremove
apt-get install wget
apt-get install sysv-rc-conf
apt-get install nfs-common
apt-get install ntp
dpkg-reconfigure tzdata
~~~~
I chose to have the directory <span class="code">/home/weewx</span> on a separate SD flash card, formatted with a Linux ext2 file system, which I then plugged into the slot the SheevaPlug offers.

To have it mounted automatically on reboot, add the following line to /etc/fstab:
~~~~
/dev/mmcblk0p1 /home/weewx ext2 defaults 0 1
~~~~
I then went about adding the weewx prerequisites in the normal way (see the <span class="code">weewx</span> [User&#39;s Guide](http://www.weewx.com/docs/usersguide.htm) file):
~~~~
apt-get install sqlite3
      apt-get install python-pysqlite2
      apt-get install python-configobj&nbsp; # this installs python-support as well
      apt-get install python-serial
      apt-get install python-cheetah
      apt-get install python-imaging&nbsp;&nbsp;&nbsp; # for PIL; it&#39;s not included on the SheevaPlug
~~~~
Weewx then fired up right away.

The SheevaPlug is no speed demon, but it&#39;s easily fast enough for the job at hand. Downloading the initial two weeks of data from the memory of my VantagePro console took about 10 minutes. After that,file generation went quite fast. Generating 30 images takes about 6.7 seconds, compared to 10.2 seconds on my 
fit-PC. Generating 4 HTML files takes 1.8 seconds on the Plug vs. 1.9 seconds on the fit-PC.

# 3. Update

11/23/2012. I've noticed that a lot of the links in my original notes on the SheevaPlug have been abandoned by plugcomputer.org. I've been able to find new ones, but the resultant pages don't seem to be maintained. For example, they have missing figures.

&copy; [Copyright](http://www.weewx.com/docs/copyright.htm) Tom Keffer