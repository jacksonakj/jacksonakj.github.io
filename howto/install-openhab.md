---
layout: page
title: Install Latest Mosquitto on Rasbeprry Pi (Jessie)
---

The current version that comes with Raspbian is out of date, so we will have to install from the source distribution.

### Instructions

**Install dependencies**

{% highlight bash %}
sudo apt-get update && sudo apt-get upgrade
sudo apt-get install libssl-dev cmake libc-ares-dev uuid-dev daemon
{% endhighlight %}

**Install libwebsockets**
The latest version of libwebsockets are incompatible with the latest versions of mosquitto, so this will install
a compatible version.

{% highlight bash %}
wget http://git.libwebsockets.org/cgi-bin/cgit/libwebsockets/snapshot/libwebsockets-1.4-chrome43-firefox-36.tar.gz
tar -zxf libwebsockets-1.4-chrome43-firefox-36.tar.gz
cd libwebsockets-1.4-chrome43-firefox-36
mkdir build && cd build
cmake ..
sudo make install
sudo ldconfig
{% endhighlight %}

**Install mosquitto**

-Get the source code
{% highlight bash %}
cd
git clone git://git.eclipse.org/gitroot/mosquitto/org.eclipse.mosquitto.git
cd org.eclipse.mosquitto
{% endhighlight %}

Edit the config.mk
{% highlight bash %}
nano config.mk
WITH_WEBSOCKETS:=yes
{% endhighlight %}

Make and install
{% highlight bash %}
make
sudo make install
sudo ldconfig
{% endhighlight %}

Add a user for mosquitto

{% highlight bash %}
sudo useradd -r -m -d /var/lib/mosquitto -s /usr/sbin/nologin -g nogroup mosquitto
{% endhighlight %}

**Configure Moquitto**

{% highlight bash %}
sudo mkdir /etc/mosquitto
sudo cp mosquitto.conf /etc/mosquitto/mosquitto.conf
sudo nano /etc/mosquitto/mosquitto.conf

# =================================================================
# Persistence
# =================================================================

# If persistence is enabled, save the in-memory database to disk
# every autosave_interval seconds. If set to 0, the persistence
# database will only be written when mosquitto exits. See also
# autosave_on_changes.
# Note that writing of the persistence database can be forced by
# sending mosquitto a SIGUSR1 signal.
autosave_interval 1800

# If true, mosquitto will count the number of subscription changes, retained
# messages received and queued messages and if the total exceeds
# autosave_interval then the in-memory database will be saved to disk.
# If false, mosquitto will save the in-memory database to disk by treating
# autosave_interval as a time in seconds.
#autosave_on_changes false

# Save persistent message data to disk (true/false).
# This saves information about all messages, including
# subscriptions, currently in-flight messages and retained
# messages.
# retained_persistence is a synonym for this option.
persistence true

# The filename to use for the persistent database, not including
# the path.
persistence_file mosquitto.db

# Location for persistent database. Must include trailing /
# Default is an empty string (current directory).
# Set to e.g. /var/lib/mosquitto/ if running as a proper service on Linux or
# similar.
persistence_location /tmp/


# If set to true, add a timestamp value to each log message.
log_timestamp true


# Port to use for the default listener.
port 1883

# Choose the protocol to use when listening.
# This can be either mqtt or websockets.
# Websockets support is currently disabled by default at compile time.
# Certificate based TLS may be used with websockets, except that
# only the cafile, certfile, keyfile and ciphers options are supported.
#protocol mqtt
listener 9001
protocol websockets

{% endhighlight %}

**Install Moquitto As a Service**
{% highlight bash %}
sudo nano /etc/systemd/system/mosquitto.service
{% endhighlight %}

mosquitto.service file:
{% highlight bash %}
[Unit]
Description=Mosquitto MQTT Broker daemon
ConditionPathExists=/etc/mosquitto/mosquitto.conf
After=network.target
Requires=network.target

[Service]
Type=forking
RemainAfterExit=no
StartLimitInterval=0
PIDFile=/var/run/mosquitto.pid
ExecStart=/usr/local/sbin/mosquitto -c /etc/mosquitto/mosquitto.conf -d
ExecReload=/bin/kill -HUP $MAINPID
Restart=on-failure
RestartSec=2

[Install]
WantedBy=multi-user.target
{% endhighlight %}

Enable and load the mosquitto service.
{% highlight bash %}
sudo systemctl enable mosquitto.service.
sudo systemctl daemon-reload
{% endhighlight %}

Mosquitto service commands:
{% highlight bash %}
sudo systemctl start mosquitto
sudo systemctl stop mosquitto
sudo systemctl status mosquitto
sudo systemctl restart mosquitto
{% endhighlight %}


### Verify that mosquitto is up and running

**Verify the Mosquitto service is runnning**

{% highlight bash %}
sudo systemctl enable mosquitto.service.
sudo systemctl daemon-reload
{% endhighlight %}

**Verfiy that a message can be published**

Open a terminal and run the follow to subscribe to a topic
{% highlight bash %}
mosquitto_sub -t "topic/test"
{% endhighlight %}

Open a second terminal and run the following to publish a message
{% highlight base %}
mosquitto_pub -t topic/test -m "test"
{% endhighlight %}

[buildmosquitto]: http://blog.thingstud.io/recipes/how-to-make-your-raspberry-pi-the-ultimate-iot-hub/
[systemdsetup]: http://xperimentia.com/2015/11/27/insights-from-building-an-mqtt-based-user-interface/#systemd
