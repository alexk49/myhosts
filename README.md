# set-hosts

Wrapper script for Steven Black Hosts to manage two different hosts files on a timer. This allows you to block general ads/malware constantly and block distracting sites during work hours.

# install

The script will set up and manage the Steven Black Hosts repo in .local/share so just download the script:

```
wget 
```

## config

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

## permissions

The script requires sudo permissions to make changes to the system hosts file - and to restart the Network Manager to flush the DNS cache after changing the system hosts file.

## setup

Clone the Steven Black Hosts repo and generate both a default and work hosts file:
```
set-hosts --setup
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

Once the two files have been generated through the set up, you can toggle the hosts file used as the system hosts with:

```
# set main hosts file
set-hosts --default

# set work hosts file
set-hosts --work
```

If you need to regenerate a single host file - for example after changing your custom hosts - you can run:

```
# regenerate main hosts file
set-hosts --default --get

# regenerate work hosts file
set-hosts --work --get
```

This will make the hosts file again from your current version of the Steven Black repository and re-add your custom hosts.

To completely update the Steven Black hosts repository and both hosts files run:

```
set-hosts --update
```

You will want to do this now and again to get the updated block lists.

## timers

Once you have two hosts files you are happy with you might want to make different ones get used at different times of day, for example using the .work hosts file during work hours and the main hosts file the rest of the time.
