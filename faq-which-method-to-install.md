## Should I install via setup.py or use a pre-packaged WeeWX ?

* if you are a new Linux user who doesn't want to spend a lot of time learning Linux, use the pre-packaged WeeWX
* if you are an experienced Linux user you 'might' want to consider the setup.py method
* if you are on a less frequently used os not based on Debian or RedHat operating systems, you 'likely' will need to use setup.py


### Benefits of pre-packaged WeeWX

The main benefit is the entire procedure, other than connecting up your hardware and verifying it works, is automated to a few step procedure friendly to non-Linux users.

The downside is that this follows Linux Filesystem Standards, so bits and pieces of WeeWX are sprinkled through your os, just like any other application that follows this filesystem standard.   Fortunately, pre-packaged WeeWX can also be 'removed' in one step by using the operating systems packaging commands (apt or dpkg for Debian-based systems, yum or rpm for RedHat-based systems).

A secondary downside is that upgrades ask a somewhat confusing question about how to deal with the weewx.conf file and whether it should be kept, upgraded, etc.   Unfortunately the confusing part is due to the operating system's packaging tools, so it's not anything WeeWX can make significantly less confusing at runtime.

### Benefits of using setup.py

The main benefit is that, other than the system startup file, WeeWX is totally contained in one directory `/home/weewx`.   This makes upgrading much easier, as the setup.py installer tends to be smarter than the various os-dependent packaging systems.

So one scenario to keep your old configuration and try a new version might look something like:
* stop weewx
* rename /home/weewx to something else
* install the 'new' weewx version
* start the newly installed newer weewx version
* validate your system acts as expected

If you want to revert to a previous version, simply reverse the process so /home/weewx is the version you want to run.

(note - the above hypothetical example assumes your public_html and archive trees are symlinked out to different locations outside /home/weewx, but anybody running setup.py should have the [Linux skillset](https://github.com/weewx/weewx/wiki/faq-user-expectations) to do that as a prerequisite).


## For more details

* see the [User's Guide](https://weewx.com/docs/usersguide.htm#Where_to_find_things)
* see the 'quickstart' section of the [WeeWX documentation](https://weewx.com/docs.html) home page
