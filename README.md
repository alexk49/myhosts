# myhosts

Wrapper script for Steven Black Hosts to manage two different hosts files on a timer. This allows you to block general ads/malware constantly and block distracting sites during work hours only.

The has been tested on Ubuntu 24.04.3 LTS only but linux distributions with /etc/hosts should work but the autostart/timer settings maybe different.

## install

The script will set up and manage the Steven Black Hosts repo in .local/share so just download the script:

Download the file:

```
# download script
wget https://raw.githubusercontent.com/alexk49/myhosts/refs/heads/main/myhosts -O ~/.local/bin/myhosts
# set executable permissions
chmod +x ~/.local/bin/myhosts
```

```
# download config if wanted
mkdir -p ~/.local/share/myhosts
wget https://raw.githubusercontent.com/alexk49/myhosts/refs/heads/main/myhosts.conf.example -O ~/.local/share/myhosts/myhosts.conf
```

Or clone the repo:

```
cd ~/.local/share
git clone https://github.com/alexk49/myhosts.git
```

## custom host files

The bulk of the host files are created by the Steven Black script but you can have your own custom files as well. These are kept in:

```
# custom default hosts file used by default and work
~/.local/share/myhosts/myhosts

# custom work only hosts file
~/.local/share/myhosts/myhosts.work
```

The custom host files should follow the same rules outlined in the Steven Black hosts README, so the lines should generally look like:

```
0.0.0.0 site-to-block

# e.g block reddit:
0.0.0.0 reddit.com
```

The generated hosts files will be created in the same directory:

```
# default hosts file
~/.local/share/myhosts/hosts.main
# work hosts file
~/.local/share/myhosts/hosts.work
```

Whenever you run the script to change hosts the in use file will be set in:

```
~/.local/share/myhosts/hosts
```

Which is then copied across as the main hosts file - which is backed up everytime it is changed.

```
# main system hosts file
/etc/hosts
# back up version
/etc/hosts.bak
```

## setup

Clone the Steven Black Hosts repo and generate both a default and work hosts file:
```
myhosts --setup
```

This will create the following files in ~/.local/share/myhosts:

```
hosts  hosts.main  hosts.work
```

The hosts file in this directory is just the output of the updateHostsFile.py script.

Before modifying your system hosts file, back it up:

```
sudo cp /etc/hosts /etc/hosts.org.bak
```

If the original system hosts has mappings you'd like to keep then do:

```
sudo cp /etc/hosts ~/.local/share/myhosts/myhosts

# or if you've already started making a myhosts file:
sudo cat /etc/hosts >> ~/.local/share/myhosts/myhosts
```

You can then further customise your myhosts file - that is used for both the work and default hosts file - as needed.

# usage

The script requires sudo permissions to make changes to the system hosts file - and, if the flag is passed, to restart the Network Manager to flush the DNS cache after changing the system hosts file.

Once the two files have been generated through the set up, you can toggle the hosts file used as the system hosts with:

```
# set main hosts file
myhosts --default

# set work hosts file
myhosts --work
```

If you need to regenerate a single host file - for example after changing your custom hosts - you can run:

```
# regenerate main hosts file
myhosts --default --get

# regenerate work hosts file
myhosts --work --get
```

This will make the hosts file again from your current version of the Steven Black repository and re-add your custom hosts.

To completely update the Steven Black hosts repository and both hosts files run:

```
myhosts --update
```

You will want to do this now and again to get the updated block lists.

## timers

Once you have two hosts files you are happy with you might want to make different ones get used at different times of day, for example using the .work hosts file during work hours and the main hosts file the rest of the time.

The default work hours are 09:00 to 16:30. To update these, you can either use a .config file in to specify the times or override via cli args.

```
cp myhosts.conf.example ~/.local/share/myhosts/myhosts.conf
```

You just need to specify the variables in the format:

```
# Enable weekday-only work mode
WORK_DAYS=1            # 1 = Mon–Fri only, 0 = every day

# Work hours (24h format)
WORK_START=08:00
WORK_END=17:00
```

With cli args but you must put the variable args first, before the task flag like:

```
myhosts --work-start 08:00 --work-end 16:00 --auto
```

The default location for the myhosts directory - where the host files will be generated and updated is:

```
~/.local/share/myhosts
```

and the default location for the copied Steven Black repo is:
```
~/.local/share/stevenblack-hosts/
```

These can be overriden at run time by defining the environment variables before running:

```
export MY_HOSTS_DIR=/some/custom/path
export SB_HOSTS_DIR=/some/custom/path 
```

This is useful if you want to set the script to fully run as root, which means the custom host files can only be edited or made by root.

## User timer set up

The below is the set up you can follow to keep the custom host files in the default user directory:

```
~/.local/share/myhosts
```

You can set up cronjobs like the below:

```
# open user cron
crontab -e 
```

Set up cronjobs like the below:

```
SHELL=/bin/bash

# start of work
30 8 * * 1-5 /home/you/.local/bin/myhosts --auto >> /home/you/.local/share/myhosts/myhosts.log 2>&1

# End of work hours
30 16 * * 1-5 /home/you/.local/bin/myhosts --auto >> /home/you/.local/share/myhosts/myhosts.log 2>&1

# update host files repo once a week
30 9 * * 1 /home/you/.local/bin/myhosts --update >> /home/you/.local/share/myhosts/myhosts.log 2>&1
```

An additional option is to set it to run as an autostart script. This should work as an alternative to the cron reboot.

For lxqt:

```
echo '[Desktop Entry]
Type=Application
Exec=/bin/bash -c "/home/$USER/.local/bin/myhosts --auto >> /home/$USER/.local/share/myhosts/myhosts.log 2>&1"
Hidden=false
NoDisplay=false
X-LXQt-Autostart-enabled=true
Name=myhosts
Comment=Copy hosts file at login
Version=1.0' >> ~/.config/autostart/myhosts.desktop
```

For kde:

```
echo '[Desktop Entry]
Type=Application
Exec=/bin/bash -c "/home/$USER/.local/bin/myhosts --auto >> /home/$USER/.local/share/myhosts/myhosts.log 2>&1"
Hidden=false
NoDisplay=false
X-KDE-AutostartScript=true
Name=myhosts
Comment=Copy hosts file at login
Version=1.0' >> ~/.config/autostart/myhosts.desktop
```

This will make the script run upon every login.

If running this way then you may want to have passwordless sudo set up for your user or set it just for this script by adding it as visudo entry (but updating 'you' to your actual username).

Open visudo entry:
```
sudo visudo -f /etc/sudoers.d/myhosts
```

Copy into file:

```
# Allow user to copy any hosts file from .local/share/myshare to /etc/hosts without password
you ALL=(root) NOPASSWD: /usr/bin/cp -v /home/you/.local/share/myhosts/hosts.* /etc/hosts --backup --suffix=.bak
```
