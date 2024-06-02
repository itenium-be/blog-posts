---
layout: post
author: Wouter Van Schandevijl
title: "Synology Setup"
subTitle: "How to setup your brand new Synology"
date: 2024-06-01
desc: >
  You've finally gotten around buying yourself a NAS,
  more specifically, a Synology NAS (yaye!).
  <br>
  This setup guide should get you up
  and running in no time -- aimed at those
  with a Windows background that are not afraid
  of a little CLI 😃
bigimg:
  url: synology-setup-big.png
  desc: "It's maybe just a little smaller than this!"
  origin: Midjourney
  prompt: "synology nas"
img:
  url: synology-ds916.png
  desc: "Love this one!"
categories: productivity
tags: [synology]
interesting:
  - url: https://global.download.synology.com/download/Document/Software/DeveloperGuide/Firmware/DSM/All/enu/Synology_DiskStation_Administration_CLI_Guide.pdf
    desc: "Synology DiskStation Administration CLI Guide"
  - url: https://gist.github.com/walkerjeffd/374750c366605cd5123d
    desc: "Configure Synology NAS as Git Server"
toc:
  title: Synology
  icon: server
package-versions:
  - package: DSM
    version: 7.1.1
socials:
  youtube:
  linkedin:
  instagram:
  twitter:
---

You've got yourself a brand new Synology.
Now you need ssh, git and Docker so you
can actually start using it for more than,
you know, mere file storage.

<!--more-->

# Initial Setup

Getting started is really easy, after connecting
your NAS to your local network, navigate to
[finds.synology.com](https://finds.synology.com/)
which figures out the local IP address
so you can start the DSM wizard at port 5000.

The DSM (DiskStation Manager) is their handy web UI
you can use to configure pretty much everything!

The wizard is pretty self-explanatory from there on out,
the only thing you may really want to think about
is which RAID you want to be using, for which
Synology conveniently offers you their
[RAID Calculator](https://www.synology.com/en-global/support/RAID_calculator).

## Local IP

Who can remember some random default IP?  
Control Panel > Connectivity > Network:  
- Network Interface > LAN 1 > Edit > IPv4 > Use manual configuration
- General > Server name: Remember that your DSM is also available as "serverName:5000"!


## External Access

You will want to access your Synology even when not
on your local network. Signing into a Synology Account
is a necessary step here:  
Control Panel > Services > Synology Account

After that:  
- QuickConnect > Enable QuickConnect: Which sets up an url like `QuickConnect.to/serverName`
- DDNS > Add: Keep your dynamic ISP IP in sync with your Synology
  - Service Provider > Synology: Free `serverName.synology.me` (or other!) domain name.
  - Enable Heartbeat: Get email notifications when your Synology goes down/back up.


## Other options

Some other options you may want to tweak right away:

- Control Panel > System > Notification > Email > Enable email notifications (ex for pwd reset, heartbeat, ...)
- Control Panel > Security > Security > Logout timer (default is 15min)



# User Setup

## Passwordless Sign-In

Configure passwordless sign-in from the <i class="fa fa-user"></i>
icon at the top right of the DSM and pick "Account" and then
either "2-Factor Authentication" or "Passwordless Sign-In".

Download the [Synology Secure SignIn](https://play.google.com/store/apps/details?id=com.synology.securesignin&pli=1)
app or use one you already have like Google Authenticator.

Some other interesting options there:
- Others > Resume DSM to my previous logout status when signed in
- Display Preferences > Date Format: because MM/dd/YYYY is just plain weird 😉
- Account > Change Password: Synology will not accept your [44 bits of entropy](https://xkcd.com/936/) password
  - Control Panel > User & Group > Advanced > Apply password strength rules


# SSH

First, activate your `~/` home:  
Control Panel > File Sharing > User & Group > Advanced > User Home > Enable user home service

Then, enable SSH:  
Control Panel > Terminal & SNMP > Terminal > Enable SSH Service

If you keep the default port 22 and make it externally available,
expect a LOT of login attempt notifications from script kiddies.  
You may want to pick a random port instead! Which has it's
[own problems](https://serverfault.com/a/619945).

You may also want to look at Auto Block settings at  
Control Panel > Connectivity > Security > Protection

You can now login with your password:

```sh
ssh 192.168.1.x -p 22 -luserName
```

If this fails with error: "unable to start ssh-agent service, error :1058":  

```powershell
Get-Service -Name ssh-agent | Set-Service -StartupType Manual
```

Or start in `services.msc`: OpenSSH Authentication Agent



## RSA Key Pairs

Who wants to type that password all the time...


On your Windows (!?) machine:

```powershell
ssh-keygen

# You could also use DSM to upload the pub file...
cd $env:userprofile/.ssh
scp -P 22 -r ./id_rsa.pub userName@192.168.1.x:~/
```

On your NAS:

```shell
mkdir ~/.ssh
cat ~/id_rsa.pub >> ~/.ssh/authorized_keys
rm ~/id_rsa.pub

chmod 755 ~/
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys

sudo sed -i '/^#PubkeyAuthentication yes/s/^#//' /etc/ssh/sshd_config
sudo synosystemctl restart sshd.service
```

Check out [this blog post](https://blog.aaronlenoir.com/2018/05/06/ssh-into-synology-nas-with-ssh-key/)
for more details.


## Oh My Bash!

The out of the box ssh experience of Synology is ok.
But we can do better...

This basically involves configuring a `~/.bashrc`.

I opted for [Oh My Bash](https://github.com/ohmybash/oh-my-bash).

```sh
bash -c "$(curl -fsSL https://raw.githubusercontent.com/ohmybash/oh-my-bash/master/tools/install.sh)"
```

If you have forked the Oh My Bash repository:

```sh
cd ~/.oh-my-bash
git remote add mine https://github.com/Laoujin/oh-my-bash
git fetch mine
git branch -u mine/master
git pull
```


This created said `~/.bashrc` which you will want to configure!
- `OSH_THEME`: Pick a folder name in `themes`
- `completions`: Tab autocomplete? Yes please!
- `aliases`: You get `...` as `cd ../../` out of the box but there are many more.
- `plugins`: bashmarks (=jump to location) is really cool! Run `bm` for help.

Use `bash -l` to reload your shell after having made changes.


<!--
TODO: A separate blog post on Oh My Bash?
Configuring your own theme:
Available colors:

# export COLOR_NC='\e[0m' # No Color
# export COLOR_BLACK='\e[0;30m'
# export COLOR_GRAY='\e[1;30m'
# export COLOR_RED='\e[0;31m'
# export COLOR_LIGHT_RED='\e[1;31m'
# export COLOR_GREEN='\e[0;32m'
# export COLOR_LIGHT_GREEN='\e[1;32m'
# export COLOR_BROWN='\e[0;33m'
# export COLOR_YELLOW='\e[1;33m'
# export COLOR_BLUE='\e[0;34m'
# export COLOR_LIGHT_BLUE='\e[1;34m'
# export COLOR_PURPLE='\e[0;35m'
# export COLOR_LIGHT_PURPLE='\e[1;35m'
# export COLOR_CYAN='\e[0;36m'
# export COLOR_LIGHT_CYAN='\e[1;36m'
# export COLOR_LIGHT_GRAY='\e[0;37m'
# export COLOR_WHITE='\e[1;37m'
-->


# Package Center

You probably want to install these packages.
From the UI because `synopkg` doesn't seem
to provide a way to install an official
package 😭

- Text Editor
- Git Server
- Docker
- Cloud Sync (Dropbox, GDrive, ...)

After that I basically do everything with Docker
but if you want some ready-made packages that
are not in the official listing, be sure to
checkout [SynoCommunity](https://synocommunity.com/).

## Cloud Sync

I created a `/volume1/Dropbox` shared folder for Dropbox:

```sh
sudo synoshare --help
sudo synoshare --add Dropbox "Dropbox Cloud Sync" /volume1/Dropbox "" "users" "" 0 0
```


## Git

### Configuation

```sh
git config --global user.name ""
git config --global user.email ""

# And then I copy my ".common.gitconfig" file:
# https://github.com/Laoujin/dotfiles/blob/master/config/git/.common.gitconfig
# Windows
scp -P 22 -r ~/.common.gitconfig userName@192.168.1.x:~/

# NAS
git config --global include.path "~/.common.gitconfig"
```

You could also opt to create a symbolic link from your Cloud Sync `.gitconfig`.

```sh
ln -s /volume1/Dropbox/.common.gitconfig ~/.common.gitconfig
git config --global include.path "~/.common.gitconfig"
```

### Github

For private repos and pushing stuff:

```sh
# On NAS this time!
ssh-keygen
```

Add the SSH Authentication key in [Github Settings](https://github.com/settings/keys):
- New SSH key
- Paste the `.pub` file



## Docker

To avoid having to `sudo docker` everything.

```sh
sudo synogroup --add docker
sudo synogroup --member docker $USER
sudo chown root:docker /var/run/docker.sock
```


## Text Editor

It's good for editing a config file here or there
but also not for much more than that!


### Visual Studio Code

In case you don't like VIM 😀

```yaml
version: '3'

services:
  openvscode-server:
    image: lscr.io/linuxserver/openvscode-server:latest
    container_name: openvscode-server
    environment:
      - PUID=1026
      - PGID=100
      - TZ=Europe/Brussels
      - CONNECTION_TOKEN= #optional
      - CONNECTION_SECRET= #optional
      - SUDO_PASSWORD=password #optional
      - SUDO_PASSWORD_HASH= #optional
    volumes:
      - ~/docker-configs/VSCode:/config
      - /volume1/Projects:/projects
    ports:
      - 3000:3000
    restart: unless-stopped
```
