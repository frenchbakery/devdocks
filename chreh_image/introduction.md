# Chreh's RPiOS Image

On January 10th 2023, chreh (an active member on the KIPR developer discord) published a image of modern RPiOS Desktop with the newest version of all the KIPR software installed on it. This includes:

 - Qt6 version of BotUI
 - refactored libwallaby (libkipr)
 - harrogate
 - any dependencies (libkar, pcompiler)

## Installation

You can download chreh's image from one of these drive links:
 - Version 1? (some initial version) https://drive.google.com/file/d/18gXBm63qkNM-x8Gv5KHnpxVoRj3bXq41/view?usp=share_link
 - Version 4.2 https://drive.google.com/drive/folders/1rOJVQ_L3yA-URBi9XMyCg6bpgSSppcuO

Once you have the file, unzip it to retrieve the image (it is quite large). Then you can install that image using Raspberry Pi Imager or another tool just like any other Raspberry Pi OS image. The only difference is that it has the KIPR software preinstalled and it is already configured to work with the screen.

The only thing you might want to do is to set the HDMI drive mode to DVI in order to get rid of some pixel lines on the side(s) of the display. To do so, add this line to ```/boot/config.txt```:

```ini
hdmi_drive=1

# make sure hdmi_drive is not specified anywhere else in the file already
```

## Functionality and information

The new versions of the image (after v1) will automatically start Harrogate and BotUI after boot using the ```/etc/xdg/autostart/ihsstartup.desktop``` target. It runs the script ```/etc/sbin/ihsstart``` which does all the magic.

These images also contain patches to libkipr and BotUI that aren't in the KIPR repository jet. You can find the repositories and patches to libraries on Chreh's [GitHub account](https://github.com/chrehall68). The [wombatimg](https://github.com/chrehall68/wombatimg) has all repositories linked as submodules.