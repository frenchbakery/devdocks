# RPiOS Installation

This section will guide you through the process of installing a fresh and modern version of Raspberry Pi OS (Lite) on the Wombat and getting it's peripherals to work properly.

## Requirements 

 - An SD Card to install the OS onto
 - A Computer with internet access and an SD slot/reader
 - Raspberry Pi Imager installed on the computer

## OS Selection

When creating a custom image, you can select any version of Raspberry Pi OS that suits your needs. However, there are a few limitations and recommendations.

First of all, the official Wombat OS is based on a (very old) 32-Bit version of Raspberry Pi OS. In order to get all of the legacy KIPR Software to work without too much hacking around, you will have to use a 32-Bit version as well. It is to be noted that there are discussions about updating to a newer version in KIPR internally, however it looks like that is not going to happen any time soon. Still, some versions of the software (like the [refactored libkipr](install_libkipr_rpios.md) that unfortunately isn't default jet) do work on 64-Bit RPiOS. Whenever that is the case, it is noted in our guide pages.

When it comes to Full vs. Desktop vs. Lite, we recommend going with the Lite version of Raspberry Pi OS as it drastically reduces the amount of bloat that is present on the system by default and you will probably never need. Any additional software (like a window manager and display server) can be selectively installed later in case you need them.

This installation guide applies both to 32-Bit and 64-Bit versions of Raspberry Pi OS Lite, so it should be trivial to select what you need.

## Installing the OS

First of all, use the Raspberry Pi Imager to install the desired image onto your SD card.  Before flashing the image, go to the settings and configure the default user, e.g. "access" with PWD "access".

Once the Image is flashed, don't put it in the Wombat straight away, because the screen doesn't work out of the box. To configure the screen, open the config.txt file on the boot partition. While doing so, we can also configure some other peripherals.

### Configure screen

In the config.txt file, find the following lines:

```ini
# uncomment to force a console size. By default it will be display's size minus
# overscan.
#framebuffer_width=1280
#framebuffer_height=720

# uncomment if hdmi display is not detected and composite is being output
#hdmi_force_hotplug=1

# uncomment to force a specific HDMI mode (this will force VGA)
#hdmi_group=1
#hdmi_mode=1

# uncomment to force a HDMI mode rather than DVI. This can make audio work in
# DMT (computer monitor) modes
#hdmi_drive=2
```

Replace this block with the following text. This will configure the screen size and HDMI mode to properly output Video on the integrated display:

```ini
# uncomment to force a console size. By default it will be display's size minus
# overscan.
framebuffer_width=800#1280
framebuffer_height=480#720

# uncomment if hdmi display is not detected and composite is being output
hdmi_force_hotplug=1

# uncomment to force a specific HDMI mode (this will force VGA)
#hdmi_group=1
#hdmi_mode=1
hdmi_group=2
hdmi_mode=87
hdmi_cvt=800 480 60 6 0 0 0
hdmi_ignore_edid=0xa5000080

# uncomment to force a HDMI mode rather than DVI. This can make audio work in
# DMT (computer monitor) modes
#hdmi_drive=2
hdmi_drive=1
```

### Enabling interfaces

To use all Wombat features, we need SPI and I2C. These can be enabled by finding the following lines:

```ini
# Uncomment some or all of these to enable the optional hardware interfaces
#dtparam=i2c_arm=on
#dtparam=i2s=on
#dtparam=spi=on
```

and uncommenting the two mentioning i2c and spi:

```ini
# Uncomment some or all of these to enable the optional hardware interfaces
dtparam=i2c_arm=on
#dtparam=i2s=on
dtparam=spi=on
```

<br>

You should now be able to plug the SD card into the Wombat and start it. It should boot and display a login console on the screen. 

We have experienced lines of random pixels on the left and top of the screen and were not able to fix this jet. If you have any ideas, open a PR.