#Pogoplug Bluetooth Speaker

Setup Pogoplug to receive and play audio stream over Bluetooth from any mobile device. Galaxy S5 is used a s music source in this project

#Installation

##Assumption

* Your login id is 'blue'
* Audio USB, Bluetooth USB shoud be detected by system

```
lsusb
Bus 001 Device 004: ID 0d8c:0008 C-Media Electronics, Inc. 
Bus 001 Device 003: ID 1131:1001 Integrated System Solution Corp. KY-BT100 Bluetooth Adapter
```

##Audio USB dongle

###As root

```
pacman -Syu alsa-utils alsa-lib
gpasswd -a blue audio
```

###As blue

```
<Log out, then log back in again after gpasswd command>
alsamixer # set the volume to max
speaker-test # You should hear sound!
```

###As root

```
pacman -Syu pulseaudio pulseaudio-alsa pulseaudio-bluetooth
useradd -m pulse
gpasswd -a pulse audio
```

Create system file for pulseaudio

```
/etc/systemd/system/pulseaudio.service
[Unit]
Description=pulseaudio service
Requires=bluetooth.target

[Service]
User=pulse
ExecStart=/usr/bin/pulseaudio -v
Restart=always
LimitRTPRIO=99
LimitNICE=40
LimitMEMLOCK=40000

[Install]
WantedBy=multi-user.target
```

Install system file

```
systemctl enable pulseaudio
systemctl start pulseaudio
systemctl status pulseaudio
```

##Bluetooth USB dongle

###As root

```
pacman -Syu bluez bluez-libs bluez-utils
gpasswd -a blue lp
gpasswd -a pulse lp
systemctl enable bluetooth
systemctl start bluetooth
hciconfig hci0 up
hciconfig hci0 class 0x200420
```

###Pairing

```
agent KeyboardOnly
default-agent
discoverable on
pairable on
trust [dev]
```

###Auto start Bluetooth USB on reboot

```
/etc/udev/rules.d/10-bluetooth.rules
ACTION=="add", KERNEL=="hci0", RUN+="/usr/bin/hciconfig hci0 up"
```

#References

* https://wiki.archlinux.org/index.php/Bluetooth
* https://wiki.archlinux.org/index.php/PulseAudio
* https://delx.net.au/blog/2014/01/bluetooth-audio-a2dp-receiver-raspberry-pi/
