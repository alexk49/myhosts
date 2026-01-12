# myhosts

Wrapper script for Steven Black Hosts to manage two different hosts files on a timer. This allows you to block general ads/malware constantly and block distracting sites during work hours only.

The has been tested on Ubuntu 24.04.3 LTS only but linux distributions with /etc/hosts should work but the autostart/timer settings maybe different.

## install

The script will set up and manage the Steven Black Hosts repo in .local/share so just download the script:

```
wget 
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

The script requires sudo permissions to make changes to the system hosts file - and to restart the Network Manager to flush the DNS cache after changing the system hosts file.

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

The default work hours are 09:00 to 16:30. To update these, you can either place a .config file in ~/.local/share/myhosts to specify the times:

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

Or these can be overriden with cli args but you must put the variable args first:

```
myhosts --work-start 08:00 --work-end 16:00 --auto
```

You can set up a cronjobs like the below:

```
crontab -e 

# Start of work hours
0 9 * * 1-5 /home/you/.local/bin/myhosts --auto

# End of work hours
30 16 * * 1-5 /home/you/.local/bin/myhosts --auto

# run on reboot
@reboot /home/you/.local/bin/myhosts --auto

# update host files repo once week
30 9 * * 1 /home/you/.local/bin/myhosts --update 
```

An additional option is to set it to run as an autostart script. This should work as an alternative to the cron reboot.

```
echo '[Desktop Entry]
Exec=/home/"$USER"/.local/bin/set-hosts --auto"
Icon=dialog-scripts
Name=myhosts
Type=Application
X-KDE-AutostartScript=true' >> ~/.config/autostart/myhosts.desktop
```

This will make the script run upon every login.

If running this way then you may want to have passwordless sudo set up for your user or set it just for this script by adding it as visudo entry (but updating 'you' to your actual username):

```
sudo visudo -f /etc/sudoers.d/myhosts

you ALL=(root) NOPASSWD: \
    /home/you/.local/bin/myhosts, \
    /usr/bin/cp, \
    /usr/bin/systemctl restart NetworkManager
```

