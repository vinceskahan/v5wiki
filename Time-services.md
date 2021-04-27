It is not possible to create a dependable data logging system without an accurate source of time. There are two parts of the problem:

1. An onboard "real time clock" (RTC)
2. A service that synchronizes the clock with a definitive time source on the internet.

This note is about the second part of that problem: using a time service. There are two common alternatives:

1. [`ntp`](http://www.ntp.org/);
2. [`systemd-timesyncd`](https://wiki.archlinux.org/index.php/systemd-timesyncd), a light weight, client-only, alternative.

*You should usually enable one or the other, but not both.*

## `ntp`

To see if your system is already running `ntp` use

    systemctl status ntp

On Debian systems, it can be easily installed using `apt`:

    sudo apt install ntp

## `systemd-timesyncd`
This is an alternative, available on operating systems using systemd. It has the advantage of being smaller and simpler. To see if you are already running `systemd-timesyncd`:

    systemctl status systemd-timesyncd

If not, it can be installed using `apt`:

    sudo apt install systemd-timesyncd

To see its status:

    timedatectl status

To enable it:

    sudo timedatectl set-ntp true

## What if I try to run both ?

One nuance on most debian-based systems is that NTP will be used if it is installed and enabled even 'if' you have systemd time services also enabled, but in general you should disable the systemd time service if you want to use the standalone NTP daemon.
