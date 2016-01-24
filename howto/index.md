---
layout: page
title: OpenHab
permalink: /howto/
---
# Install OpenHab on a Raspberry Pi

### Prepare the SD card with Rasbian
The GUI is not necessary for OpenHab, so Raspbian Lite is all that is needed.

[SD Card - Linux Instructions][1]

### Configure Raspbian
Use `raspi-config` to configure the operating system.
1. Expand Filesystem
2. Change default password
3. Change the hostname to `HabHub`.  Under `Advanced Options`
4. Enable SSH. Under `Advanced Options` > `SSH`
4. Change locale to `en_US.UTF-8 UTF-8`. Under `Internationalization Options` > `Locale`
5. Change timezone.
5. Reboot

The default user is `pi` with the password `raspberry`.

If the keyboard is outputting strange characters set the XKBLAYOUT in `/etc/default/keyboard`
```
sudo nano /etc/default/keyboard
XKBLAYOUT=”us”
sudo reboot
```

### Networking Setup
#### Ethernet

#### WiFi

Get a list of Wifi networks using `sudo iwlist wlan0 scan | grep SSID`

Add your network to `wpa_supplicant.conf` then restart wifi and check for an ip address using `iconfig wlan0`
```
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf

network={
    ssid="The_ESSID_from_earlier"
    psk="Your_wifi_password"
}

sudo ifdown wlan0
sudo ifup wlan0
ifconfig wlan0
```


### Update the operating system.

```
sudo apt-get update
sudo apt-get upgrade
```

### SSH Setup
**Disable DNS for SSH**

```
sudo nano /etc/ssh/sshd_config

UseDNS no

sudo service ssh reload
```

**Add your public SSH key.**

Paste your public key into ~/.ssh/authorized_keys.  The public key should all be on the same line.

```
mkdir ~/.ssh

nano ~/.ssh/authorized_keys
```





[1]: https://www.raspberrypi.org/documentation/installation/installing-images/linux.md
