# Assign symbolic link for auto-mounting USB devices by udev

# Introduction
Linux kernel usually just assigns unpredictable device names. We would need udev rule to make it predictable.
In technical, it can generate custom & specifiy symblic links, based on Vid, Pid, plugged-in USB port number...etc.  

In this document, we will describe our rules and define the naming convensions.


# The XU3/XU4 diagram and USB ports

The ODROID-XU3 USB2.0 sockets (A-D)
![USB Sockets](/xu3-usb2.0 ports.png)

The ODROID-XU4 USB3.0 sockets (A-B)
![USB Sockets](/xu4-usb3.0 ports.png)



# Port Assignments

The ODROID-XU3 USB2.0 sockets (A-D)
![Corresponding XU3 USB 2.0Sockets](https://docs.google.com/drawings/d/16_H_gjvYTfrIBuZyytr0LjA5_RZo3IF8GQLxZzwaYr8/pub?w=769&h=275)

The ODROID-XU4 USB3.0 sockets (A-B)
![Corresponding XU4 USB 3.0 Sockets](https://docs.google.com/drawings/d/1Uotxm_qWHY_9kqP2Xrq2ourmvdrzX8semviyzw4FU80/pub?w=769&h=275)


# The Symbolic links design

10-port USB Hub
![10-port USB hub picture](https://github.com/Muchun-Yen/Assign-symbolic-links-for-auto-mounting-USB-devices-by-udev/blob/master/USBHub-10p.jpg)

7-port USB Hub
USBHub-7p-black.jpg
![7-port USB hub picture](https://github.com/Muchun-Yen/Assign-symbolic-links-for-auto-mounting-USB-devices-by-udev/blob/master/USBHub-7p-black.jpg)

Using 2 USB Hub 
![7-port-A USB hub picture](https://github.com/Muchun-Yen/Assign-symbolic-links-for-auto-mounting-USB-devices-by-udev/blob/master/USBHub-7p-A.jpg)


#### Check out the Ardurino Mega2560 board PID/VID, and USB connect sorcket infomation (connect to socket A)

## This is the example for 10-port USB hub case

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
#### Check out the Ardurino Mega2560 board PID/VID, and USB connect sorcket infomation (connect to port1 of the 10-port USB hub which had connected to socket A)
```javascript
  looking at device '/devices/12110000.usb/usb1/1-1/1-1.2/1-1.2.4/1-1.2.4:1.0/tty/ttyACM0':
    ...
    KERNEL=="ttyACM0"
    ...
    ATTRS{devpath}=="1.2.4"
    ATTRS{idVendor}=="2341"
    ATTRS{idProduct}=="0042"

```
## Single MEGA2560 in base (connected to port1 of the USB hub)
It will be assigned to mega_base0
udev rule will match VID=2341, PID=0042, and Port number 1.2.4

## Two MEGA2560 in Left Arm
It will be assigned to mega_arm_left0
udev rule will match VID=2341, PID=0042, and Port number 1.2.3

It will be assigned to mega_arm_left1
udev rule will match VID=2341, PID=0042, and Port number 1.2.1.1


## Two MEGA2560 in right Arm
It will be assigned to mega_arm_right0
udev rule will match VID=2341, PID=0042, and Port number 1.2.1.4

It will be assigned to mega_arm_right1
udev rule will match VID=2341, PID=0042, and Port number 1.2.1.3

#### The other ports of the USB hub (Reserved)
mega_in_hub6 
udev rule will match VID=2341, PID=0042, and Port number 1.2.1.2

mega_in_hub7 
udev rule will match VID=2341, PID=0042, and Port number 1.2.2.4

mega_in_hub8 
udev rule will match VID=2341, PID=0042, and Port number 1.2.2.3

mega_in_hub9 
udev rule will match VID=2341, PID=0042, and Port number 1.2.2.2



## create a udev rule file in /etc/udev/rules.d/;

/etc/udev/rules.d/99-mega2560-usb_hub_10port.rules
```javascript
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.4", SYMLINK+="mega_base0"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.3", SYMLINK+="mega_arm_left0"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.1", SYMLINK+="mega_arm_left1"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.4", SYMLINK+="mega_arm_right0"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.3", SYMLINK+="mega_arm_right1"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.2", SYMLINK+="mega_in_hub6"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.2.4", SYMLINK+="mega_in_hub7"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.2.3", SYMLINK+="mega_in_hub8"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.2.2", SYMLINK+="mega_in_hub9"
```  
/etc/udev/rules.d/99-mega2560-usb_hub.rules

/etc/udev/rules.d/99-mega2560-usb_hub_7port.rules
```
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.4", SYMLINK+="mega_base0"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.3", SYMLINK+="mega_arm_left0"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.4", SYMLINK+="mega_arm_left1"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.3", SYMLINK+="mega_arm_right0"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.2", SYMLINK+="mega_arm_right1"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.2", SYMLINK+="mega_in_hub6"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.1, SYMLINK+="mega_in_hub7"
```

/etc/udev/rules.d/99-mega2560-usb_hub_7port-a.rules
```
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.4", SYMLINK+="mega_base"
KERNEL=="ttyUSB*", ATTRS{idProduct}=="2303", ATTRS{idVendor}=="067b", ATTRS{devpath}=="1.2.1.3", SYMLINK+="xv11"
KERNEL=="ttyUSB*", ATTRS{idProduct}=="2303", ATTRS{idVendor}=="067b", ATTRS{devpath}=="1.2.4", SYMLINK+="gyro"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.2", SYMLINK+="mega_head"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.2.1.4", SYMLINK+="mega_arm_left0"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.2.1.3", SYMLINK+="mega_arm_left1"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.2.4", SYMLINK+="mega_arm_right0"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2.1.2.3", SYMLINK+="mega_arm_right1"
```


The udev rule makes those Symbolic links (mega_*)to ttyACM* which corresponding to each mounted Mega2560 boards in USB hub.
The udev rule makes those Symbolic links (xv11)to ttyUSB* which corresponding to USB hub port 1.2.1.3 
The udev rule makes those Symbolic links (gryo)to ttyUSB* which corresponding to USB hub port 1.2.4 




# References

ODROID-XU3 EVM board (http://www.hardkernel.com/main/products/prdt_info.php?g_code=G140448267127)

Adurino Mega2560 boards (https://www.arduino.cc/en/Main/ArduinoBoardMega2560)

udev (https://www.kernel.org/pub/linux/utils/kernel/hotplug/udev/udev.html)