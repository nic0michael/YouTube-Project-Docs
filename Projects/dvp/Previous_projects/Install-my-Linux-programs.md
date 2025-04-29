---
title: "Install My Linux Programs"
date: 2023-02-07T16:55:31Z
draft: false
---

```
###### To upgrade to Linux Mint
sudo apt update
sudo apt install mintupgrade


# To launch the Upgrade Tool:
sudo mintupgrade

# When the tool is done and the upgrade is successful uninstall it and reboot your computer.
sudo apt remove mintupgrade
sudo reboot

###### get Ubuntu version
lsb_release -a

## add 32 bit acrhitecture for a 64bit machine
sudo dpkg --add-architecture i386;sudo apt-get update

sudo apt update && sudo apt upgrade

sudo apt install ubuntu-restricted-extras
sudo apt install gcc
sudo apt install gedit
sudo apt install aptoncd
#sudo update-pepperflashplugin-nonfree
sudo apt install traceroute
sudo apt install rar
sudo apt install zip
sudo apt install locate
sudo apt install partitionmanager
sudo partitionmanager
sudo apt-get install -y simple-scan
###sudo apt install snapd DONT
###sudo apt install flatpack DONT
###sudo apt install gnome-software-plugin-flatpak DONT
sudo apt install python3-pip
sudo apt install pulseaudio


sudo apt install gphoto2 libgphoto2*

##### gEDA and Gerber Viewer
sudo apt install geda pcb gerbv

##### Fritzing
sudo apt install fritzing fritzing-data fritzing-parts

## Free CAD
sudo apt-get install freecad


## install pip on Python3
sudo apt install python3-pip
pip3 --version

## instal Thonny Python Ide
sudo apt install thonny

## Install Retext MD Editor
pip3 install ReText

#################### important things ##################################
sudo dpkg --add-architecture i386; sudo apt-get update
apt-get install libgphoto2-6sudo apt install obs-studio

##### Disable Automatic Updates from Command Line
sudo nano /etc/apt/apt.conf.d/20auto-upgrades


########## Microsoft Core Fonts
sudo apt-get install msttcorefonts

########## Executive Report Fonts
apt install fontconfig-config libfreetype6

############# PDF PRINTER
sudo apt-get install printer-driver-cups-pdf
The pdf printer provided by that package will "print" the resulting PDFs into the /home/[user]/PDF directory.

Games
=========
## TRIGGER ALLY
apt install trigger-rally
apt install flightgear
apt install supertuxkart
apt install torcs


LogitecWingman
===============
https://pc-freak.net/blog/configure-joystick-gamepad-debian-ubuntu-mint-gnu-linux/
sudo apt install jstest-gtk
```
