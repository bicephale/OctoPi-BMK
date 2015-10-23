# BiPi Documentation

This is the official documentation for the Bicephale 3D Printer Raspberry Pi Image.

## Infos

Login and password are system wide

**Login :** bmk
**Password :** bicephale

## Network

Netconnectd seems to override (or disable) native DHCP daemon, need to edit **/etc/network/interfaces** and set eth0 to

    iface eth0 inet dhcp

    sudo nano /etc/dhcp/dhclient.conf

Then set the **timeout** value to something convenient.



BiPi basic cooking
===================


If you want to cook your very own version of **BiPi** follow the basic steps below wich are preliminary to an up and running system.

----------

Minibian Setup
-------------

First, you'll have to setup Minibian on your Raspberry Pi 2. Download & install it to the SD card.

Follow this guide for the initial setup http://www.htpcguides.com/lightweight-raspbian-distro-minibian-initial-setup/

During the first boot, you'll have to do some basic steps in the **raspi-config** utility

> **Note:**

> - Expand filesystem
> - Enable camera
> - Enable boot to console
> - Set GPU memory to **128mb** (otherwise, camera won't work)
> - Change hostname to bicephale

Sometimes, you could have an error message during the first boot wich is :

> Kernel lacks cgroups or memory controller not avaiable, not starting cgroups

Here is a solution to fix it :

> sudo nano /boot/cmdline.txt

Then, add this line **before** elevator=deadline

> cgroup_enable=memory

Then change the classic Pi username with the command below. Be carefull, you'll have to do this with a screen, it won't work over SSH.

    exec sudo -s
    cd /
    usermod -l bmk -d /home/bmk -m pi
    reboot

Change the default password, by purpose, we'll use a convention everywhere, username **bmk** password **bicephale** Use the command below to change the password :

    passwd

OctoPrint setup
-------------

Please setup Octoprint using this guide https://github.com/foosel/OctoPrint/wiki/Setup-on-a-Raspberry-Pi-running-Raspbian be carefull to use the Bicephale's OctoPrint fork repo instead of the classic one.

In the part relative to setup scripts please change in /etc/default/octoprint the user to bmk (instead of Pi) and executable path to DAEMON=/home/bmk/OctoPrint/venv/bin/octoprint

Netconnectd setup
-------------

Install netconnectd following this guide https://github.com/foosel/netconnectd follow the first steps (till install netconnectd) to prepare the system. Once it's done, please update HOSTAPD from Adafruit (https://learn.adafruit.com/setting-up-a-raspberry-pi-as-a-wifi-access-point/install-software) with the steps below :

    wget http://adafruit-download.s3.amazonaws.com/adafruit_hostapd_14128.zip
    unzip adafruit_hostapd_14128.zip
    sudo mv /usr/sbin/hostapd /usr/sbin/hostapd.ORIG
    sudo mv hostapd /usr/sbin
    sudo chmod 755 /usr/sbin/hostapd

Then, to test if it's working, create hostapd config file in /tmp/hostapd.conf (JUST FOR TESTING PURPOSES)

    interface=wlan0
    driver=rtl871xdrv
    ssid=Bicephale
    channel=3
    wpa=3
    wpa_passphrase=bicephale              
    wpa_key_mgmt=WPA-PSK
    wpa_pairwise=TKIP CCMP
    rsn_pairwise=CCMP

And run the command below :

    sudo /usr/sbin/hostapd /tmp/hostapd.conf

You should have an AP up and running. Then, install the netconnect daemon

https://github.com/foosel/netconnectd

Then edit /etc/netconnectd.yaml change AP name and driver name to rtl871xdrv

Then install plugin https://github.com/OctoPrint/OctoPrint-Netconnectd and make sure that you previously source /venv/bin/activate before installing (to be in the same OctoPrint's Python environement)

Check /etc/network/interfaces and re enable eth0 dhcp

Misc
-------------

Autmatic startup MJPStreamer https://github.com/foosel/OctoPrint/issues/441

PySerial 250000 baudrate https://github.com/foosel/OctoPrint/wiki/OctoPrint-support-for-250000-baud-rate-on-Raspbian

Custom boot screen
http://www.raspberry-projects.com/pi/pi-operating-systems/raspbian/custom-boot-up-screen

Custom splash screen instructions (alternate) http://www.edv-huber.com/index.php/problemloesungen/15-custom-splash-screen-for-raspberry-pi-raspbian

## Licence

Unless otherwise specified, everything in this repository is covered by the following licence:

[![Creative Commons Attribution-ShareAlike 4.0 International](https://licensebuttons.net/l/by-sa/4.0/88x31.png)](http://creativecommons.org/licenses/by-sa/4.0/)

***Raspberry Pi Documentation*** by the [Raspberry Pi Foundation](https://www.raspberrypi.org/) is licensed under a [Creative Commons Attribution 4.0 International Licence](http://creativecommons.org/licenses/by-sa/4.0/).

Based on a work at https://github.com/bicephale/documentation
