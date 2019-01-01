---
title: "RasPi Cluster (2): OS setup"
date: 2015-04-14 22:06:10
tags: [raspi, cluster]
categories: [Raspberry Pi]
---

{% asset_img IMG_3705.jpg %}

After contructing the hardware, the next step is to install Operating System.

## Raspberry OS

There are quite a lot of choices of Operating Systems for Raspberry Pi. As we can see in the official [download page](https://www.raspberrypi.org/downloads/):

- NOOBS: Raspian and possible to net-install other OS
- [Raspbian](http://raspbian.org/): `Debian` distribution
- Snappy `Ubuntu` Core
- [PIDORA](http://fedoraproject.org/wiki/Remix): `Fedora` distribution
- [OPENELEC](http://wiki.openelec.tv/index.php?title=Installing_OpenELEC_on_Raspberry_Pi): Media Center
- OSMC: Media Center
- [Raspbmc](http://kodi.tv/): another media center

And other personal/3rd party distribution:

- [HypriotOS](http://blog.hypriot.com/post/hypriotos-back-again-with-docker-on-arm/): `Raspbian` with `docker` enabled
- [dietRaspbian](https://github.com/geerlingguy/diet-raspbian): minimized `Raspbian`
- [`Arch` Linux ARM](http://archlinuxarm.org/platforms/armv6/raspberry-pi)
- [OpenWrt](http://wiki.openwrt.org/toh/raspberry_pi): a router OS

Source: [http://elinux.org/RPi_Distributions](http://elinux.org/RPi_Distributions)

<!-- more -->

## Selection critieria

There are some simple critiera help me to select an image:

1. Popular distribution: can find useful tutorial, helpful information
2. Docker ready: easy to deploy an application
3. As lite as possible, no GUI: as served as cluster no GUI required
4. Newest linux kernel

In this case, I took full advantage of [HypriotOS](http://blog.hypriot.com/post/hypriotos-back-again-with-docker-on-arm/) which has docker pre-built-in and some friendly modification as: support for network hotplugging, bash completion for Docker commands, image works for Pi 1 & 2.

## Flash image to Micro SD

**These steps are suitable for Mac OS X only.**

Open `Terminal` application, or in my case [iTerm2]().

Then begin of `Script Dancing`:

```bash
# to find the right disk
diskutil list
# unmount the SD card (my case disk2)
diskutil unmountdisk /dev/disk2
# goto the image folder
cd Downloads
# Flash with `DD`, this step will take ~10mins
sudo dd if=hypriot-rpi-20150329-140334.img of=/dev/disk2 bs=2m
```

Or use a [GUI tool](https://alltheware.wordpress.com/2012/12/11/easiest-way-sd-card-setup/).

For linux/windows installation, please check [this](http://computers.tutsplus.com/articles/how-to-flash-an-sd-card-for-raspberry-pi--mac-53600).

## Start of Login

Next plugin all MicroSD cards in the Raspberry Slot.

{% asset_img IMG_3707.jpg %}

Now ready to go!

Finding the IP address is not an easy task, at least without the right tool. Download [fing](http://www.overlooksoft.com/download), don't worry, it's free for searching all equipements within the network. And it's very obvious to find all the raspberry.

Once having the IP, login with `ssh username@ip-addr` command.

e.g. for an ip `192.168.0.2` and an user `pi`, type `ssh pi@192.168.0.2`.

For HypriotOS, here is the username and password:


| Username | Password     |
| ------   | ------------ |
| pi       | raspberry    |
| root     | hypriot      |


{% asset_img login.png %}

Once logged-in the first thing is to expand the partition to all space of SD card. This can be done by typing `sudo raspi-config`.

{% asset_img raspi_config.png %}

Select the first item and `enter`, `enter`. The system is ready to take full space of SD card.

Type `df -h` to check if success.

Then the next thing is to `update` and `upgrade`.

```bash
sudo apt-get update
sudo apt-get upgrade
```

## Optional Steps

### Upgrade to Jessie

The current stable `debian` release is `Wheezy` which offers only a compile-ready R in version `2.15.1`. For a better compatibility and performance I need  a `3.x.x` R. The [testing release](https://www.debian.org/releases/jessie/index.en.html) `Jessie` should be fine, and by the way it will be table release on [april 25, 2015](https://lists.debian.org/debian-devel-announce/2015/03/msg00016.html).

Here is the steps ([source](http://linuxconfig.org/raspbian-gnu-linux-upgrade-from-wheezy-to-raspbian-jessie-8)):

```bash
# Update apt sources list
sudo sed -i 's/wheezy/jessie/g' /etc/apt/sources.list
# check correct setting for apt sources list
cat /etc/apt/sources.list
```

result should be:

```
deb http://mirrordirector.raspbian.org/raspbian jessie main firmware contrib non-free
```

Once you start the actual upgrade you will be given a choice to manually restart any currently running services. Manually restart services is recommended as this give you an option to restart services selectively eg. SSH etc. Furthermore, if you are performing the update via SSH and have no physical access to your Raspberry PI make sure not to disable SSH root access.

```bash
# Hit the new list
sudo apt-get update
# upgrade & dist-upgrade
sudo apt-get upgrade
sudo apt-get dist-upgrade
# reboot is required
sudo reboot
```

### Fix for Perl warning setting locale failed on Raspbian

While doing sudo apt-get upgrade or install, I always get these warning message:

```bash
perl: warning: Setting locale failed.
perl: warning: Please check that your locale settings:
        LANGUAGE = (unset),
        LC_ALL = (unset),
        LC_TIME = "fr_FR.UTF-8",
        LC_MONETARY = "fr_FR.UTF-8",
        LC_ADDRESS = "fr_FR.UTF-8",
        LC_TELEPHONE = "fr_FR.UTF-8",
        LC_NAME = "fr_FR.UTF-8",
        LC_MEASUREMENT = "fr_FR.UTF-8",
        LC_IDENTIFICATION = "fr_FR.UTF-8",
        LC_NUMERIC = "fr_FR.UTF-8",
        LC_PAPER = "fr_FR.UTF-8",
        LANG = "en_GB.UTF-8"
    are supported and installed on your system.
perl: warning: Falling back to the standard locale ("C").
locale: Cannot set LC_ALL to default locale: No such file or directory
```

The solution via [here](http://daker.me/2014/10/how-to-fix-perl-warning-setting-locale-failed-in-raspbian.html):

```bash
export LANGUAGE=en_US.UTF-8
export LANG=en_US.UTF-8
export LC_ALL=en_US.UTF-8
locale-gen en_US.UTF-8
dpkg-reconfigure locales
```

This will do the trick.

If the system can't find `dpkg-reconfigure`, try to install `sudo apt-get install debconf`. If it said it is installed, then it is located in `/usr/sbin/dpkg-reconfigure`, the reason is that the directory is not in the `$PATH`.

### Passwordless SSH

Sometimes passwordless SSH simplifies the life and furthermore the cluster requires passwordless SSH.

Here are the steps:

1. Generate rsa Key in client side.
2. Create a directory `~/.ssh` in server side
3. Transfer your public key from client to server
4. Test login

```bash
# 1. Generate rsa Key
ssh-keygen -t rsa
# 2. Make directory on server
ssh b@B mkdir -p .ssh
# 3. Transfer public key
cat .ssh/id_rsa.pub | ssh b@B 'cat >> .ssh/authorized_keys'
# 4. Test that
ssh b@B
```
