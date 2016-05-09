#Pogoplug Bluetooth Speaker

Setup Pogoplug to receive and play audio stream over Bluetooth from any mobile device. Galaxy S5 is used a s music source in this project

#Installation

##Assumption
Your login id is 'blue'

##Audio USB dongle

###As root
pacman -S alsa-utils alsa-lib

gpasswd -a blue audio

###As you
<Log out, then log back in again>
alsamixer # set the volume to max

speaker-test # You should hear sound!

##Bluetooth USG dongle

###As root
pacman -Syu bluez bluez-libs bluez-utils

gpasswd -a blue lp

gpasswd -a pulse lp

systemctl enable bluetooth

systemctl start bluetooth

hciconfig hci0 up

hciconfig hci0 class 0x200420



###Pairing

agent KeyboardOnly
default-agent
discoverable on
pairable on
trust [dev]

#References
* https://wiki.archlinux.org/index.php/Bluetooth
* https://wiki.archlinux.org/index.php/PulseAudio
* https://delx.net.au/blog/2014/01/bluetooth-audio-a2dp-receiver-raspberry-pi/
