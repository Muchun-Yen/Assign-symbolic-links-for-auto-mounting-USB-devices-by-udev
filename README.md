# Assign symbolic link for auto-mounting USB devices by udev

# Introduction
The Linux system will auto-mounting USB device when user plug-in a usb device or reboot system.
Sometimes the mounted name will make people misunderstand because the USB mounted sequentially different.

Setting a udev rule can make symbolic links for the mounted USB devices.
therefore the mounted USB device can be assigned a symbolic link which is corresponding to the USB socket.


## An example in ODROID-XU3

The ODROID-XU3 USB2.0 sockets (A-D)
![USB Sockets](/xu3-usb2.0 ports.png)


Three Adurino Mega2560 connect to

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

#### create a udev rule file in /etc/udev/rules.d/;

/etc/udev/rules.d/99-mega2560-sequence.rules
```javascript
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.2", SYMLINK+="mega-0"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.3", SYMLINK+="mega-1"
KERNEL=="ttyACM*", ATTRS{idProduct}=="0042", ATTRS{idVendor}=="2341", ATTRS{devpath}=="1.4", SYMLINK+="mega-2"
```  

The udev rule makes those Symbolic links (mega-0 ~ mega-1)to ttyACM*(ttyACM0 ~ttyACM2)which corresponding to each mounted Mega2560 board in USB socket (A-C).



# References

ODROID-XU3 EVM board (http://www.hardkernel.com/main/products/prdt_info.php?g_code=G140448267127)

Adurino Mega2560 boards (https://www.arduino.cc/en/Main/ArduinoBoardMega2560)

udev (https://www.kernel.org/pub/linux/utils/kernel/hotplug/udev/udev.html)