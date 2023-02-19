# Use SPI without root

The libwallaby library uses the SPI interface to communicate to the STM32 microcontroller in the Wombat in order to control the IOs (via. SPI DMA). On the Raspberry Pi, the SPI interface is accessed via sysfs as the file ```/dev/spidev0.0```.

In order to access this file, root privileges are required by default. This problem, because because the KIPR software runs as root. When creating a custom SSH user and a custom build system though, it would be advantageous to be able to access the device without entering the sudo password every time.

## Sources

This guide was written according to the steps from this website: https://forum.up-community.org/discussion/2141/solved-tutorial-gpio-i2c-spi-access-without-root-permissions

## Setup for Wombat OS

Wombat OS already has a group "spi" and a udev rule allowing it to access SPI devices. Therefore, the only thing that needs to be done is to add the desired user to this group. We will use our custom "access" user.

```bash
sudo adduser access spiuser
# alternatively you can add the current user to the group:
sudo adduser "$USER" spiuser
```

## Setup from scratch

On a fresh install of linux, you may need to add the group and udev rule manually.

### Group setup

Create a user group that will be allowed to access the device. We will call the group "spiuser".

```bash
sudo groupadd spiuser
```

Next, add whatever user you want to be able to access the SPI device to the group. We will use our custom "access" user.

```bash
sudo adduser access spiuser
# alternatively you can add the current user to the group:
sudo adduser "$USER" spiuser
```

### udev rule

To change the owner of hardware devices, a new udev rule has to be added. Create the file ```/etc/udev/rules.d/50-spi.rules``` (you can use a different number if 50 is already taken) and add the following content:

```js
SUBSYSTEM=="spidev", GROUP="spiuser", MODE="0660"
```

This will change the owner group of all SPI devices to "spiuser".

After a reboot, everything should work as expected!