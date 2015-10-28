# Assign symbolic link for auto-mounting USB devices by udev

# Introduction
Linux kernel usually just assigns unpredictable device names. We would need udev rule to make it predictable.
In technical, it can generate custom & specifiy symblic links, based on Vid, Pid, plugged-in USB port number...etc.  

In this document, we will describe our rules and define the naming convensions.


# The XU3 diagram and USB ports

The ODROID-XU3 USB2.0 sockets (A-D)
![USB Sockets](/xu3-usb2.0 ports.png)


# Port Assignments

![Corresponding Sockets](https://docs.google.com/drawings/d/16_H_gjvYTfrIBuZyytr0LjA5_RZo3IF8GQLxZzwaYr8/pub?w=769&h=275)

#### Check out the Ardurino Mega2560 board PID/VID, and USB connect sorcket infomation (connect to socket A)
```javascript
$ udevadm info -a -n ttyACM0
```
The follows information are we need.
```javascript

  looking at device '/devices/12110000.usb/usb1/1-1/1-1.2/1-1.2:1.0/tty/ttyACM0':
    ...
    KERNEL=="ttyACM0"
    ...
    ATTRS{devpath}=="1.2"
    ATTRS{idVendor}=="2341"
    ATTRS{idProduct}=="0042"
    ...
```   

## MEGA2560 in Head
It will be assigned to mega-0
udev rule will match VID=2341, PID=0042, and Port number 1.2

## MEGA2560 in Arms
It will be assigned to mega-1
udev rule will match VID=2341, PID=0042, and Port number 1.3

## MEGA2560 in base
It will be assigned to mega-2
udev rule will match VID=2341, PID=0042, and Port number 1.4


#### create a udev rule file in /etc/udev/rules.d/;

/etc/udev/rules.d/99-mega2560-sequence.rules
```javascript
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2", SYMLINK+="mega-0"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.3", SYMLINK+="mega-1"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.4", SYMLINK+="mega-2"
```  

The udev rule makes those Symbolic links (mega-0 ~ mega-1)to ttyACM*(ttyACM0 ~ttyACM2)which corresponding to each mounted Mega2560 board in USB socket (A-C).


# To be continue (Other assign..)
## XV11 sensor


# References

ODROID-XU3 EVM board (http://www.hardkernel.com/main/products/prdt_info.php?g_code=G140448267127)

Adurino Mega2560 boards (https://www.arduino.cc/en/Main/ArduinoBoardMega2560)

udev (https://www.kernel.org/pub/linux/utils/kernel/hotplug/udev/udev.html)