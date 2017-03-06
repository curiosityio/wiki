---
name: Android dev setup
---

# Needed for 64-bit Ubuntu-based distros. Used for aapt.
# From: https://stackoverflow.com/questions/19523502/androids-aapt-not-running-on-64-bit-ubuntu-13-10-no-ia32-libs-how-can-i-fix
sudo apt-get install -y libc6-i386 lib32stdc++6 lib32gcc1 lib32ncurses5
sudo apt-get install -y lib32z1

# for sqliteman which is awesome for testing out sqlite commands.
sudo apt-get install -y sqliteman

# for KVM fast emulators
# first following this guide: http://goo.gl/J3Ir0W from Intel. If not sure your machine can run KVM, follow that guide.
sudo apt-get install -y qemu-kvm libvirt-bin ubuntu-vm-builder bridge-utils
sudo adduser $USER kvm
sudo adduser $USER libvirtd
sudo apt-get install -y libsdl1.2debian:i386
# Now to run the emulator, use command:
# $ <SDK directory>/tools/emulator-x86 -avd Your_AVD_Name -qemu -m 2047 -enable-kvm

sudo mkdir -p /etc/udev/rules.d/
sudo touch /etc/udev/rules.d/51-android.rules

sudo chmod a+rx /etc/udev/rules.d/51-android.rules
# -------------------------------------------------
# this requires manual editing.
# Add the following to: /etc/udev/rules.d/51-android.rules and make sure it is readable by all: (chmod a+r)
# came from http://wiki.cyanogenmod.org/w/UDEV and allows working with older android devices on Linux
#Acer
SUBSYSTEM=="usb", ATTR{idVendor}=="0502", MODE="0664", GROUP="plugdev"
#ASUS
SUBSYSTEM=="usb", ATTR{idVendor}=="0b05", MODE="0664", GROUP="plugdev"
#Dell
SUBSYSTEM=="usb", ATTR{idVendor}=="413c", MODE="0664", GROUP="plugdev"
#Foxconn
SUBSYSTEM=="usb", ATTR{idVendor}=="0489", MODE="0664", GROUP="plugdev"
#Fujitsu & Fujitsu Toshiba
SUBSYSTEM=="usb", ATTR{idVendor}=="04c5", MODE="0664", GROUP="plugdev"
#Garmin-Asus
SUBSYSTEM=="usb", ATTR{idVendor}=="091e", MODE="0664", GROUP="plugdev"
#Google
SUBSYSTEM=="usb", ATTR{idVendor}=="18d1", MODE="0664", GROUP="plugdev"
#Haier
SUBSYSTEM=="usb", ATTR{idVendor}=="201e", MODE="0664", GROUP="plugdev"
#Hisense
SUBSYSTEM=="usb", ATTR{idVendor}=="109b", MODE="0664", GROUP="plugdev"
#HTC
SUBSYSTEM=="usb", ATTR{idVendor}=="0bb4", MODE="0664", GROUP="plugdev"
#Huawei
SUBSYSTEM=="usb", ATTR{idVendor}=="12d1", MODE="0664", GROUP="plugdev"
#K-Touch
SUBSYSTEM=="usb", ATTR{idVendor}=="24e3", MODE="0664", GROUP="plugdev"
#KT Tech
SUBSYSTEM=="usb", ATTR{idVendor}=="2116", MODE="0664", GROUP="plugdev"
#Kyocera
SUBSYSTEM=="usb", ATTR{idVendor}=="0482", MODE="0664", GROUP="plugdev"
#Lenovo
SUBSYSTEM=="usb", ATTR{idVendor}=="17ef", MODE="0664", GROUP="plugdev"
#LG
SUBSYSTEM=="usb", ATTR{idVendor}=="1004", MODE="0664", GROUP="plugdev"
#Motorola
SUBSYSTEM=="usb", ATTR{idVendor}=="22b8", MODE="0664", GROUP="plugdev"
#MTK
SUBSYSTEM=="usb", ATTR{idVendor}=="0e8d", MODE="0664", GROUP="plugdev"
#NEC
SUBSYSTEM=="usb", ATTR{idVendor}=="0409", MODE="0664", GROUP="plugdev"
#Nook
SUBSYSTEM=="usb", ATTR{idVendor}=="2080", MODE="0664", GROUP="plugdev"
#Nvidia
SUBSYSTEM=="usb", ATTR{idVendor}=="0955", MODE="0664", GROUP="plugdev"
#OTGV
SUBSYSTEM=="usb", ATTR{idVendor}=="2257", MODE="0664", GROUP="plugdev"
#Pantech
SUBSYSTEM=="usb", ATTR{idVendor}=="10a9", MODE="0664", GROUP="plugdev"
#Pegatron
SUBSYSTEM=="usb", ATTR{idVendor}=="1d4d", MODE="0664", GROUP="plugdev"
#Philips
SUBSYSTEM=="usb", ATTR{idVendor}=="0471", MODE="0664", GROUP="plugdev"
#PMC-Sierra
SUBSYSTEM=="usb", ATTR{idVendor}=="04da", MODE="0664", GROUP="plugdev"
#Qualcomm
SUBSYSTEM=="usb", ATTR{idVendor}=="05c6", MODE="0664", GROUP="plugdev"
#SK Telesys
SUBSYSTEM=="usb", ATTR{idVendor}=="1f53", MODE="0664", GROUP="plugdev"
#Samsung
SUBSYSTEM=="usb", ATTR{idVendor}=="04e8", MODE="0664", GROUP="plugdev"
#Sharp
SUBSYSTEM=="usb", ATTR{idVendor}=="04dd", MODE="0664", GROUP="plugdev"
#Sony
SUBSYSTEM=="usb", ATTR{idVendor}=="054c", MODE="0664", GROUP="plugdev"
#Sony Ericsson
SUBSYSTEM=="usb", ATTR{idVendor}=="0fce", MODE="0664", GROUP="plugdev"
#Teleepoch
SUBSYSTEM=="usb", ATTR{idVendor}=="2340", MODE="0664", GROUP="plugdev"
#Toshiba
SUBSYSTEM=="usb", ATTR{idVendor}=="0930", MODE="0664", GROUP="plugdev"
#ZTE
SUBSYSTEM=="usb", ATTR{idVendor}=="19d2", MODE="0664", GROUP="plugdev"
