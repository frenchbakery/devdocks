# Setting up a WiFi AP without NetworkManager on RPiOS

Wombat OS creates it own WiFi network, so you can easily connect to it without a wire. This section guides you through recreating this functionality on a custom Raspberry Pi OS (Lite) installation.

## Requirements

 - A Wombat with a modern version of Raspberry Pi OS installed on the SD card (ideally [configured to work with the Wombat screen](rpios_installation.md))
 - Network and Internet access on the Wombat (e.g. using ethernet on the local network)

You have to be connected via LAN to setup WiFi AP mode.

## Sources

We have used the following website to write this guide:
https://raspberrypi-guide.github.io/networking/create-wireless-access-point

## Installation

The installation is quite simple, just two packages. One serves as a DHCP server while the other one enables the AP mode

```bash
sudo apt install dnsmasq hostapd
```

## IP configuration

First, we need to configure the IP address for the Pi and tell dhcpcd that we don't want to use wpa_supplicant on this interface, as we don't want to connect to any external network. To do this, add the following to the end of ```/etc/dhcpcd.conf``` (you need root privileges to access the file).

We will use the default IP setup that is also used by Wombat OS

```
interface wlan0
static ip_address=192.168.125.1/24
nohook wpa_supplicant
```

Restart dhcpcd for changes to take effect:

```bash
sudo systemctl restart dhcpcd.service
```

## DHCP config

Add the following to the end of ```/etc/dnsmasq.conf```:

```ini
interface=wlan0
dhcp-range=192.168.4.2,192.168.4.20,255.255.255.0,24h
```

Then enable the service for autostart:

```bash
sudo systemctl enable dnsmasq
```

## WiFi Setup

Create a file called ```/etc/hostapd/hostapd.conf``` and add the following content to it:

```ini
country_code=DE
interface=wlan0
ssid=<your serial nr>-wombat
channel=9
auth_algs=1
wpa=2
wpa_passphrase=<your psk>
wpa_key_mgmt=WPA-PSK
wpa_pairwise=TKIP CCMP
rsn_pairwise=CCMP
```

Replace ```<your serial nr>``` with your serial number and ```<your psk>``` with a password of your liking. We recommend using the same password that comes with your default Wombat OS installation.

Open the file ```/etc/defaults/hostapd``` and find the following line:

```ini
#DAEMON_CONF=""
```

Uncomment it and add the location of the above created config file in the quotation marks. It should look like this:

```ini
DAEMON_CONF="/etc/hostapd/hostapd.conf"
```

Now, enable the service for autostart and start it up:

```bash
sudo systemctl unmask hostapd.service
sudo systemctl enable hostapd.service
sudo systemctl start hostapd.service
```