This page provides answers to some of the most frequently-asked questions (FAQ) about WeeWX.

## Getting started

### I need help - where do I go for support?
* read the rest of the questions in this page!
* read the WeeWX documentation at `weewx.com` (https://weewx.com/docs)
* read the guides in the WeeWX wiki (https://github.com/weewx/weewx/wiki)
* read the [instructions](Help!-Posting-to-weewx-user) about posting to the `weewx-user` group
* post your question(s) to the `weewx-user` group (https://groups.google.com/group/weewx-user)
* if you find any of this confusing, please [let us know](https://groups.google.com/group/weewx-user) so that we can make it better!

### Is my hardware supported by WeeWX?

WeeWX itself includes support for a small number of weather station models. However, many, many more weather stations, as well as other types of hardware, are supported by user-supplied `drivers` - extensions to the WeeWX core.

The stations supported in WeeWX core are listed in the [hardware guide](https://weewx.com/docs/5.0/hardware/drivers/).

Many more weather stations are listed at the [supported hardware page](http://weewx.com/hardware.html).  There you will see pictures and model numbers.  As you browse the devices on that page, remember that many devices are sold under different model numbers, by different vendors. Support for these are provided through `drivers` that you must add after you install WeeWX.

To see all of the drivers, browse the [*Drivers* section](https://github.com/weewx/weewx/wiki#drivers) of the WeeWX wiki.

### Is WeeWX hard to install and use?

The answer to this depends on you. Many tens of thousands of people have installed WeeWX. Some of those people have years of computer experience, but many of them have little or no experience with computers, or command-line programs.

WeeWX is a command-line application, which means that people used to 'point-and-click' interfaces frequently have some growing pains. Using a command-line application requires some investment of your time and energy to reach a minimal level of expertise. However, the skills you learn to install and run WeeWX are applicable to many other systems.

### What skills do I need to run WeeWX?

At a minimum you must:
* be willing to type
* understand what files and directories are, as well as how to navigate and view them
* be able to open, edit, and save a text file
* understand how to start and stop processes
* have a basic understanding of [permissions](faq-permission-denied), and when/if to use 'sudo'

There are many instructions and guides that will help you, but you must still think.

### How do I learn the things I need to run WeeWX?

There are *dozens* of free Linux introductory courses available on Internet.   The [Introduction to Linux](https://www.edx.org/course/introduction-to-linux) is a free, self-paced course on edx.org, which has been taken by hundreds of thousands of people.  Or try the [Linux Journey](https://linuxjourney.com/)

There are many os-specific forums on Reddit that are Linux, Linux administration, and operating system specific.
* [I am new to Linux, what should a beginner know?](https://www.reddit.com/r/linux4noobs/comments/10kz0xh/i_am_new_to_linux_what_should_a_beginner_know/)
* [A good guide to learn Linux from zero](https://www.reddit.com/r/linuxquestions/comments/125dn50/a_good_guide_to_learn_linux_from_zero/)

A few minutes of searching will turn up tutorials specific to your operating system of choice, whether that is MacOS, FreeBSD, Rocky, Debian, Mint, Ubuntu, Redhat, SUSE, etc.

### Which method should I use to install WeeWX?

There are three ways to install WeeWX: the packaged approach, the Python approach, and the run-from-source approach.

The packaged approach (DEB/RPM packages using `apt`, `yum/dnf`, or `zypper`) is the fastest, easiest way to get started with WeeWX. However, this will work only if you use one of the operating systems for which these packages have been created.

The Python approach (`pip` in WeeWX V5, or `setup.py` in WeeWX V4) will work on every operating system. However, this approach requires you to install additional pieces to integrate WeeWX completely into your system. This approach is typically used by those who want to do a lot of customization, or by those who use an operating system for which there is no DEB/RPM package.

The run-from-source approach (using `git`) works on every operating system. However, this approach requires the most work to integrate WeeWX into your system. This approach is typically used by developers, or by those who need to fit WeeWX into systems with little memory or storage.

The Installation section of the User's Guide has more details.


## Registering your station

### Should I register my station?

WeeWX has an *optional* capability to register your station with the `weewx.com` server. Registered stations appear on the [map](https://weewx.com/stations.html). The registration includes only basic information about a station, including the location, hardware type, driver, WeeWX version, and Python version. We use this information to answer questions like "how many sites use this driver" and "how many sites are on the current weewx version" and the like, and to consider which configurations are used so much (or little) to merit higher or lower support levels.

Registration is *not* required.  In fact, registration is disabled by default; you must take action to register your station.

To register your station, see the instructions in the [`station_registry`](http://www.weewx.com/docs/usersguide.htm#station_registry) section of the User's Guide.

### Can I register without specifying an Internet-facing website ?

Yes, but... While you have to specify *something* for the `station_url`, it does not need to actually resolve to a real, public-facing URL. If you use a private URL, this will result in you providing your registration data (thank you), put your station on the map, but the link to your station on the map will be broken.

### How do I unregister ?

Simply disable the registration setting then restart WeeWX. Systems that refresh their data are dropped from the map in about a month.


### Why do I get "command not found" when I type WeeWX commands?

* [Command not found](faq-command-not-found) - need for a correct $PATH


### What do I do when I get "permission denied"?

* [Permission denied](faq-permission-denied) - need to use sudo


### What do these Python errors mean?

* [I'm getting a python error](faq-python-error) - python3 compatibility issues


### What does "ModuleNotFoundError" mean?

* [ModuleNotFoundError](PYTHONPATH-and-ModuleNotFoundError) - need to set PYTHONPATH


### Why do my web pages not appear as expected?

* My [web pages are not appearing](faq-web-pages-not-appearing) in my browser as expected


### How do I fix obviously incorrect data from my station?

* How do I [exclude obviously incorrect data](faq-exclude-incorrect-data) emitted by my station


### How do I answer the questions from `apt` or `yum` when I upgrade WeeWX?

* How do I answer the [questions apt-get asks](faq-questions-apt-get-asks) when I upgrade WeeWX


### How do I run `weewxd` directly in order to diagnose problems?

* I'm having [problems downloading the apt repo key](faq-apt-key-problems) to my debian(ish) system


### How do I fix GPG and problems with the apt/yum/suse repository keys?

* How to [run weewxd directly](faq-running-weewxd-directly) for debugging problems


### Where do I find the log?

* [where are my logs](faq-where-are-my-logs) ?


### What should I know about using WeeWX on a Raspberry Pi

* [Raspberry Pi](faq-raspi-nuances) nuances for new users


### FAQs about popular skins and extensions

* [How do I do XXXX in the Belchertown skin?](faq-belchertown-pointers)
* How do I fix [rtl_433 file-not-found errors](faq-rtl-433-file-not-found) in the SDR driver?



### General questions

* [About WeeWX](faq-about-weewx) - quick description of the software and its minimal requirements
* [User expectations](faq-user-expectations) - what computing skills are required to install and run WeeWX
* [How to report a problem](faq-how-to-report-a-problem) - what is needed for people to be able to help you
* Installation via package or setup.py - [which method to choose](faq-which-method-to-install) ?
* Please [register your station](faq-register-your-station) (optional) - help us understand the user community

