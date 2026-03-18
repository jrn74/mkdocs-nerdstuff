<h1>Debian Config</h1>
_tested on:_  
_HARDWARE: Raspberry Pi 4/5_  
_Software: Trixie v13_  

Update firmware
rpi-update


#Disable WiFi & Bluetooth
add to [all] in /boot/firmware/config.txt
dtoverlay=disable-wifi
dtoverlay=disable-bt

