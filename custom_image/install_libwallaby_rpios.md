# Installing libwallaby on RPiOS

This section will guide you through installing libwallaby (the older, and currently main version of the KIPR HAL library) on a generic Raspberry Pi OS (Lite) installation.

## Requirements

 - A Wombat with a modern version of 32-Bit Raspberry Pi OS installed on the SD card (ideally [configured to work with the Wombat screen](rpios_installation.md))
 - Network and Internet access on the Wombat (e.g. using ethernet on the local network)
 - git and CMake (install using ```sudo apt install git cmake```)

## Workspace setup 

To get started, ssh into your Wombat and create/navigate to a folder you wish to use for installation. Then clone the [kipr/libwallaby](https://github.com/kipr/libwallaby) or [frenchbakery/libkipr](https://github.com/frenchbakery/libkipr) repository:

```bash
mkdir -p kipr_installs
cd kipr_installs
git clone https://github.com/frenchbakery/libkipr
cd libkipr
```

This library version is in the master branch ([commit 6cefbf2687b745b627588f5b713f358da711bb5b](https://github.com/frenchbakery/libkipr/commit/6cefbf2687b745b627588f5b713f358da711bb5b) at the time of writing)

## Dependencies

Unfortunately, this version of the source tree is intended for Raspbian Jessie and some things have changed since then. Therefore some dirty hacks have to be performed to be able to compile.

First of all, make sure that the camera interface of the Raspberry Pi is enabled using ```sudo raspi-config```. We also need SPI (and possibly I2C) but those should already have been enabled during the [OS installation](rpios_installation.md). This step may require a reboot.

After that is done, update the OS to install any new dependencies: 

```bash
sudo apt update
sudo apt upgrade
```

Now we come to the dirty stuff. The CMake setup of libwallaby requires several Raspberry Pi specific libraries (shared objects) to be present under the ```/opt/vc/lib/``` folder. In recent OS versions, these have been moved to ```/usr/lib/arm-linux-gnueabihf/```. The ```/opt/``` directory is now empty. To fix this, we need to create a symbolic link to the new location so the build system can find the dependencies: 

```bash
sudo mkdir -p /opt/vc/lib
sudo ln -s /usr/lib/arm-linux-gnueabihf /opt/vc/lib
```

Now we are almost ready. Before you can build the library, you need to install some dependencies. These are documented on the repository README.md.

```bash
sudo apt install libzbar-dev libopencv-dev libjpeg-dev python-dev doxygen swig
```

## Compiling

At this point, you can simply follow the instructions found in the README.md that we have reworded below.

Create a build directory, generate the build files using CMake and start the build using make:

```bash
mkdir build
cd build
cmake ..
make
```

This takes a few minutes and may occasionally spit out some warnings but that shouldn't be an issue. It should exit with no errors.

## Installation

The binary and headers have now been prepared, however they still have to be copied to the correct system locations.

To install the library you simply need to run the install target with root privileges.

```bash
# we are still in the build folder: libwallaby/build
sudo make install
```

## Compiling a programm

This version of the library should be compatible header-wise with the one that comes with Wombat OS except for that it has more features and more headers. Standard stuff like motors, I/O, servos, ... should be identical:

```cpp
#include <kipr/servo.hpp>
#include <kipr/motors.hpp>
#include <kipr/digital.hpp>
#include <kipr/util.hpp>
```

To link your code, instead of using the ```-lwallaby``` compiler flag like with the default library, you now need to specify the path to the library with your build sources. This is likely not a intended change, but rather something that I just can't figure out.

If you wanted to compile the single-file project "main.cpp" to a "main" binary with the standard library on Wombat OS, you would need to do the following:

```bash
g++ -lwallaby -o main main.cpp
```

With the self-compiled library, the call would change to:

```bash
g++ -o main main.cpp /usr/lib/libkipr.so
```
