---
layout: page
title: Prepare Raspberry Pi for OpenHab
---
## Prepare the SD card with Rasbian

The GUI is not necessary for OpenHab, so Raspbian Lite is all that is needed.

[SD Card - Linux Instructions][1]

## Configure Raspbian

Use `raspi-config` to configure the operating system.

1.  Expand the file system

2.  Change default password

3.  Change the hostname to `HabHub`.  

    Under **Advanced Options**

4.  Enable SSH.

    Under **Advanced Options** > **SSH**

4.  Change locale to `en_US.UTF-8 UTF-8`.

    Under **Internationalization Options** > **Locale**

5.  Change timezone.

5.  Reboot

The default user is `pi` with the password `raspberry`.

If the keyboard is outputting strange characters set the XKBLAYOUT in `/etc/default/keyboard`

{% highlight bash %}
sudo nano /etc/default/keyboard
XKBLAYOUT=”us”
sudo reboot
{% endhighlight %}

## Networking Setup

### Ethernet

Coming soon.

### WiFi

Get a list of Wifi networks using `sudo iwlist wlan0 scan | grep SSID`

Add your network to `wpa_supplicant.conf` then restart wifi and check for an ip address using `iconfig wlan0`

{% highlight bash %}
sudo nano /etc/wpa_supplicant/wpa_supplicant.conf

network={
    ssid="The_ESSID_from_earlier"
    psk="Your_wifi_password"
}

sudo ifdown wlan0
sudo ifup wlan0
ifconfig wlan0
{% endhighlight %}


### Update the operating system.

{% highlight bash %}
sudo apt-get update
sudo apt-get upgrade
{% endhighlight %}

### SSH Setup
**Disable DNS for SSH**

{% highlight bash %}
sudo nano /etc/ssh/sshd_config

UseDNS no

sudo service ssh reload
{% endhighlight %}

**Add your public SSH key.**

Paste your public key into ~/.ssh/authorized_keys.  The public key should all be on the same line.

{% highlight bash %}
mkdir ~/.ssh
nano ~/.ssh/authorized_keys
{% endhighlight %}

### Install JDK 1.8 or better
1.  Download the [Linux ARM 32 Hard Float ABI][jdkdownload] Java SE Development Kit from Oracle.
2.  Extract it to /opt/java.   `sudo tar xvzf ~/jdk-8u73-linux-arm32-vfp-hflt.tar.gz -C /opt/java`
3.  Let the system know about the new version of java.

{% highlight bash %}
sudo mkdir -p -v /opt/java
sudo tar xvzf ~/jdk-8u73-linux-arm32-vfp-hflt.tar.gz -C /opt/java

sudo update-alternatives --install /usr/bin/java java /opt/java/jdk1.8.0_73/bin/java 1
sudo update-alternatives --install /usr/bin/javac j%avac /opt/java/jdk1.8.0_73/bin/javac 1

sudo update-alternatives --config java
sudo update-alternatives --config javac
{% endhighlight %}

Add JAVA_HOME environment variable
{% highlight bash %}
nano ~/.bashrc

export JAVA_HOME="/opt/java/jdk1.8.0"
export PATH=$PATH:$JAVA_HOME/bin
{% endhighlight %}

### Install openHAB

#### Download

{% highlight bash %}
wget https://bintray.com/artifact/download/openhab/bin/distribution-1.8.1-runtime.zip
wget https://bintray.com/artifact/download/openhab/bin/distribution-1.8.1-addons.zip
wget https://bintray.com/artifact/download/openhab/bin/distribution-1.8.1-demo.zip
{% endhighlight %}

#### Extract the distribution-<version>-runtime.zip to /opt/openhab
{% highlight bash %}
sudo mkdir -p -v /opt/openhab
sudo unzip distribution-1.8.1-runtime.zip -d /opt/openhab
sudo mkdir -p -v /opt/openhab/all_addons
sudo unzip distribution-1.8.1-addons.zip -d /opt/openhab/all_addons
{% endhighlight %}

#### Create openHAB configuration
{% highlight bash %}
sudo cp /opt/openhab/configurations/openhab_default.cfg /opt/openhab/configurations/openhab.cfg

{% endhighlight %}

#### Select addons
Copy add ons from `all_addons` to `addons` for support devices and features.

#### Configure openHAB

#### Configure as a service




[openhabdownload]:http://www.openhab.org/getting-started/
[jdkdownload]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[1]: https://www.raspberrypi.org/documentation/installation/installing-images/linux.md
