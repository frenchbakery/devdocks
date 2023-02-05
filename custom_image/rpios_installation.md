# RPiOS Installation

This section will guide you through the process of installing a fresh and modern version of Raspberry Pi OS (Lite) on the Wombat and getting it's peripherals to work properly.

## Requirements 

 - An SD Card to install the OS onto
 - A Computer with internet access and an SD slot/reader
 - Raspberry Pi Imager installed on the computer

## Installing the OS

First of all, use the Raspberry Pi Imager to install the desired image onto your SD card. For this guide, we will be using the 64-bit version of Raspberry Pi OS Lite. Before flashing the image, go to the settings and configure the default user, e.g. "access" with PWD "access".

Once the Image is flashed, don't put it in the Wombat straight away, because the screen doesn't work out of the box. To configure the screen, open the config.txt file on the boot partition. While doing so, we can also configure some other peripherals.

### Configure screen

In the config.txt file, find the following lines:

```ini
# uncomment the following to adjust overscan. Use positive numbers if console
# goes off screen, and negative if there is too much border
#overscan_left=16
#overscan_right=16
#overscan_top=16
#overscan_bottom=16

# uncomment to force a console size. By default it will be display's size minus
# overscan.
#framebuffer_width=1280
#framebuffer_height=720

# uncomment if hdmi display is not detected and composite is being output
#hdmi_force_hotplug=1

# uncomment to force a specific HDMI mode (this will force VGA)
#hdmi_group=1
#hdmi_mode=1
```

Replace this block with the following text. This will configure the screen size and HDMI mode to properly output Video on the integrated display:

```ini
# uncomment the following to adjust overscan. Use positive numbers if console
# goes off screen, and negative if there is too much border
overscan_left=-2
overscan_right=2
overscan_top=-2
overscan_bottom=2

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