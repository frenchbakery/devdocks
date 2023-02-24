# Touchscreen calibration using Evdev

This section will guide you through configuring the touchscreen to use the evdev driver and calibrate it using that.

## Requirements

 - A Wombat with Chreh's modern WombatOS image installed on the SD card
 - Network and Internet access on the Wombat (e.g. using ethernet on the local network)

## Installing dependencies

On modern versions of RPiOS, libinput is used as a touchscreen driver rather than evdev. To calibrate the touchscreen, we need to install evdev and set it to be the driver for the touchscreen.

First, install the driver:

```bash
sudo apt install xserver-xorg-input-evdev
```

To be able to calibrate, we need xinput_calibrator:

```bash
sudo apt install xinput-calibrator
```

## Select driver

To configure evdev to be the driver for the touchscreen, open the file ```/usr/share/X11/xorg.conf.d/40-libinput.conf``` and replace the section

```bash
Section "InputClass"
        Identifier "libinput touchscreen catchall"
        MatchIsTouchscreen "on"
        MatchDevicePath "/dev/input/event*"
        Driver "libinput"
EndSection
```

with 

```bash
Section "InputClass"
        Identifier "libinput touchscreen catchall"
        MatchIsTouchscreen "on"
        MatchDevicePath "/dev/input/event*"
#        Driver "libinput"
        Driver "evdev"
EndSection
```

This configures xorg to load evdev as the driver for touchscreens, even if the evdev class file has lower priority. 

An alternative option is to rename the file ```/usr/share/X11/xorg.conf.d/10-evdev.conf``` to have a higher priority than libinput, e.g. ```/usr/share/X11/xorg.conf.d/45-evdev.conf```. However, this has the side effect of also selecting evdev for all other input devices unless you modify the file to disable that.

## Calibrate

Now, you can calibrate the touchscreen by running 

```bash
xinit_calibrator
```

from a terminal window. You cannot use SSH to do so. 

This will spit out the content of the calibration file you can save under ```/usr/share/X11/xorg.conf.d/99-calibration.conf``` or ```/etc/X11/xorg.conf.d/99-calibration.conf```. The latter is what BotUI does, so that is probably preferable for consistency.

You can also use the calibrate button of BotUI to do this step automatically, provided BotUI has permissions to write to ```/etc/X11/xorg.conf.d/99-calibration.conf```, so for example if it is run as root.

