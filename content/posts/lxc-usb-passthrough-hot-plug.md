+++
title = "LXC USB Passthrough with hot-plug"
date = "2022-12-09T23:00:00+02:00"
author = "elraro21"
authorTwitter = "elraro21" #do not include @
tags = ["lxc", "proxmox", "container", "usb", "passthrough"]
keywords = ["lxc", "usb"]
description = "How to use USB Passthrough with hot-plug in LXC Proxmox."
showFullContent = false
+++

## LXC USB Passthrough with hot-plug in Proxmox.

Hello everyone.

I found this [post](https://monach.us/automation/connecting-zwave-stick-under-lxc/) that gave me the key to solve the hot-plug USB under LXC containers.

First of all, under `/var/lib/lxc/<ct id>/` create the folder `devices`, go in and then execute this:

  > mknod -m 666 ttyACM0 c 166 0

In my case, `ttyACM0` is my USB device (my Ender 3 printer). The second number `166` must be the same that you obtain if executed:

  > ls -la /dev/ttyACM0

When we have the correct device file, we can modify the container's config file in `/etc/pve/lxc/<ct id>`:

  > lxc.mount.entry: /var/lib/lxc/<ct id>/devices/ttyACM0 dev/ttyACM0 none bind,optional,create=file

Now just restart the container and try.

If you found problems with device permissions, you can try a udev rule `/etc/udev/rules.d/90-octoprint.rules` like this:

  > SUBSYSTEM=="tty", ATTRS{idVendor}=="0483", ATTRS{idProduct}=="5740", MODE="0666", GROUP="dialout"

Attributes `idVendor` and `idProduct` can be found with `lsusb` command:

~~~
Bus 002 Device 003: ID 152d:0578 JMicron Technology Corp. / JMicron USA Technology Corp. JMS578 SATA 6Gb/s
Bus 002 Device 001: ID 1d6b:0003 Linux Foundation 3.0 root hub
Bus 001 Device 002: ID 2109:3431 VIA Labs, Inc. Hub
Bus 001 Device 001: ID 1d6b:0002 Linux Foundation 2.0 root hub
Bus 001 Device 003: ID 0483:5740 MARLIN_STM32G0B1RE
~~~

Remember to reload udev rules with `udevadm control --reload-rules && udevadm trigger` or restart the device.

Have fun!
