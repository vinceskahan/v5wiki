## How to report a problem

We frequently see weewx-user threads with content starting with "it doesn't work" and no other details.  We can't help without more information.  This FAQ is intended to help you ask for help in a way that the many volunteer WeeWX users can be able to help you.

### What a good problem report looks like

* what are you trying to do
* what did you expect to see (or happen)
* what did you actually see (or what happened)
* what is the configuration of your system
* what did you change (if anything)


### A bad bug report

I tried to install WeeWX and it doesn't work.  Will somebody help me ?


### A good bug report

I'm trying to enable realtime updates to my Belchertown skin and it is not updating in realtime.  I keep getting 'waiting for data'.  I am running weewx 4.5.1 installed via apt-get and Belchertown 1.3.0 on a pi4 under Raspbian. My os version from /etc/os-release is 10.4


### Providing log files

Frequently you will be asked to provide logfile information to help us help you.
* edit weewx.conf, set debug = 1, save weewx.conf and restart WeeWX
* let WeeWX run for at least two archive periods (usually 10 minutes) and then take a copy of the log showing the full WeeWX startup and the 10 minutes of log activity.
* post the log extract in a text-only followup to your original message to weewx-user
* don’t restart WeeWX multiple times or post excessively long log extracts (unless expressly asked for), just post two archive periods of logfile after restarting WeeWX

Please try not to append zip files or compressed files, as that makes it more difficult for mobile users to help.  Definitely do 'not' post screen shots, as they are not searchable when the next user has your identical problem and is looking for a solution.

### Where is my logfile ?
See [here](faq-where-are-my-logs) for details

### About wee_debug

The wee_debug utility can output more detailed information about your system.  You may be asked to run that utility and follow up to your original problem report with more details.

One thing to note is that wee_debug 'tries' to obfuscate usernames/passwords/keys in weewx.conf so that posting your debug does not post private information.  You need to 'carefully' examine the outpug of wee_debug to make sure it didn't miss obfuscating anything.

<b>Known bug</b> - at this writing as of WeeWX 4.5.1 wee_debug does 'not' obfuscate anything in a commented out portion of a line.  Be very careful in examining your wee_debug output if you do things like save keys or username/passwords in comments in the file.  If you do, you will need to manually edit the wee_debug output to remove those items.

