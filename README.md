# RTKLIB for the Raspberry Pi

![Raspberry Pies](https://github.com/countxerox/RTKLIB/blob/master/img/raspberrypies.jpg)

These are my notes for configuring RTKLIB on the Rasberry Pi

Write a Raspian Jessie image to your microSD card

Connect a keyboard mouse and monitor and boot up the pi and use the GUI to set up wifi
Use the Raspbeery Pi Software Config Tool to expand the SD card file system, boot to command line instead of the desktop and also enable SSH.
$sudo raspi-config
Then reboot
...

Now you have Wifi and SSH you can disconnect the keyboard, mouse and monitor and use the pi headlessly
Do some updates
$sudo apt-get update -y
$sudo apt-get upgrade -y
...

Install some dependancies
$sudo apt-get install build-essential (probably says nothing to upgrade)
$sudo apt-get install automake 
$sudo apt-get install checkinstall 
$sudo apt-get install liblapack3gf 
$sudo apt-get install libblas3gf
...

If you want to connect the gps via the UART serial on the pi GPIO you need you remove the console
$sudo nano /boot/cmdline.txt
Change it to:
dwc_otg.lpm_enable=0 console=tty1 root=/dev/mmcblk0p2 rootfstype=ext4 elevator=deadline rootwait
(eg, remove console=ttyAMA0,115200)
...

Disable the tty service (only on Jessie):
$sudo systemctl stop serial-getty@ttyAMA0.service
$sudo systemctl disable serial-getty@ttyAMA0.service 
...

Now you can reboot for the changes to take effect and connect up the gps, for a quick test to see data coming from the gps do:
$sudo cat /dev/ttyAMA00
...

Make a clone of the SD card for when things go wrong
...

Download and copy the RTKLIB files over to the pi somehow, one way is to mount a usb stick:
$sudo mkdir /mnt/usb
$sudo mount /dev/sda1 /mnt/usb
$copy the files to pi home 
$sudo cp -r /mnt/usb/dir1 ~/

You would need the pi user to take ownership of the files
$sudo chown -R pi:pi /mydir
Remember to unmount before removing the stick:
$sudo umount /dev/sda1 /mnt/usb
...

Then you are ready to compile RTKLIB
in the app folder 
$sudo bash install.sh
...

You might need to use the following command to give access to the bash scripts 
$sudo chmod +x rtk*.sh
This one maybe for access to the  serial device
$chmod 666 /dev/ttyAMA0
...

Configure the GPS using uCenter
disable all messages
set baud rate to 115200 (the pi default serial baud rate is 115200)
make sure port is UART1
check firmware version is 2.01 ish not 3.01 or later
set rates to GPS time 1 hz
save CFG changes
(note TX is yellow wire, RX is white wire)
