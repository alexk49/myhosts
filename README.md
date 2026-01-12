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
# custom default hosts file
~/.local/share/myhosts/myhosts
# custom work hosts file
~/.local/share/myhosts/myhosts.work
```

The custom host files should follow the same rules outlined in the Steven Black hosts README, so he lines should generally look like:

```
0.0.0.0 site-to-block
# e.g block reddit:
0.0.0.0 reddit.com
```

The generated hosts files will be created in the same directory as:

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
