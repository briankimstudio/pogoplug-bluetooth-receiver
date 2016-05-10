#Pogoplug Bluetooth Speaker

Setup Pogoplug to receive and play audio stream over Bluetooth from device such as smartphone. Galaxy S5 is used a s music source in this project.

#Installation

##Assumption

* Your login id is 'blue'
* Audio USB, Bluetooth USB shoud be detected by system

```
# lsusb
Bus 001 Device 004: ID 0d8c:0008 C-Media Electronics, Inc. 
Bus 001 Device 003: ID 1131:1001 Integrated System Solution Corp. KY-BT100 Bluetooth Adapter
```

##Audio USB dongle

####As root

```
# pacman -Syu alsa-utils alsa-lib
# gpasswd -a blue audio
```

####As blue

```
<Log out, then log back in again after gpasswd command>
$ alsamixer # Set Master volume to max
$ speaker-test -c 2 # Left and right speaker should make sound alternatively
```

####As root

```
# pacman -Syu pulseaudio pulseaudio-alsa pulseaudio-bluetooth
# useradd -m pulse
# gpasswd -a pulse audio
```

Create systemd service file for pulseaudio

```
/etc/systemd/system/pulseaudio.service
[Unit]
Description=pulseaudio service
Requires=bluetooth.target

[Service]
User=pulse
ExecStart=/usr/bin/pulseaudio -v
Restart=always

[Install]
WantedBy=multi-user.target
```

Install systemd service file

```
# systemctl enable pulseaudio
# systemctl start pulseaudio
# systemctl status pulseaudio # Make sure pulseaudio service is running without any error message
```


##Bluetooth USB dongle

####As root

```
# pacman -Syu bluez bluez-libs bluez-utils
# gpasswd -a blue lp
# gpasswd -a pulse lp
# systemctl enable bluetooth
# systemctl start bluetooth
# hciconfig hci0 up
```

* Members in lp group can access bluetooth according to /etc/dbus-1/system.d/bluetooth.conf

###Pairing

```
# bluetoothctl
[bluetooth]# agent KeyboardOnly
[bluetooth]# default-agent
[bluetooth]# discoverable on
[bluetooth]# pairable on
[bluetooth]# trust [dev]
```

###Verifying a2dp is properly configured

```
[bluetooth]# show
Controller 00:11:67:12:34:56
...
	UUID: Audio Sink                (0000110b-0000-1000-8000-00805f9b34fb)
...
```
* If 'Audio Sink' doesn't show up, then, check pulseaudio configuration again

###Auto start Bluetooth USB on reboot

```
/etc/udev/rules.d/10-bluetooth.rules
ACTION=="add", KERNEL=="hci0", RUN+="/usr/bin/hciconfig hci0 up"
```

#References

* https://wiki.archlinux.org/index.php/Bluetooth
* https://wiki.archlinux.org/index.php/PulseAudio
* https://delx.net.au/blog/2014/01/bluetooth-audio-a2dp-receiver-raspberry-pi/
