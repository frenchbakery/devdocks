# Installing libkipr on RPiOS

This section will guide you through installing libkipr (the new version of libwallaby) on a generic Raspberry Pi OS (Lite) installation.

## Requirements

 - A Wombat with a modern version of Raspberry Pi OS installed on the SD card (ideally [configured to work with the Wombat screen](rpios_installation.md))
 - Network and Internet access on the Wombat (e.g. using ethernet on the local network)

## Compiling 

To get started, ssh into your Wombat and create/navigate to a folder you wish to use for installation. Then clone the [kipr/libwallaby](https://github.com/kipr/libwallaby) or [frenchbakery/libkipr](https://github.com/frenchbakery/libkipr) repository:

```bash
mkdir -p kipr_installs
cd kipr_installs
git clone https://github.com/frenchbakery/libkipr
cd libkipr
```

At the time of writing, the new version of the libwallaby library (now called libkipr) is not in the master branch jet, so you will have to use the refactor branch (commit e63f9bd5d171a6d8d095d32b3fdb3ee966bb0844 at the time of writing):

```bash
git switch refactor
git pull
```

Before you can build the library, you need to install some dependencies. These are not well documented on the official repository README.md jet. The old version of the library requires the following dependencies:


```bash
sudo apt-get install libzbar-dev libopencv-dev libjpeg-dev python-dev doxygen swig
```

However, we do not recommend installing all the dependencies if you don't need them. The new version splits the library into modules and therefore may not need all the libraries. If any essential dependencies are missing, cmake will tell you so they can be installed selectively.

Before compiling, you need to decide what functionality you wish to include. At the time of writing, these are all the documented modules that can be included:
 - `with_accel` (default: `ON`) - Build accelerometer support.
 - `with_analog` (default: `ON`) - Build analog sensor support.
 - `with_audio` (default: `ON`) - Build audio support.
 - `with_battery` (default: `ON`) - Build battery support.
 - `with_botball` (default: `ON`) - Build botball support.
 - `with_camera` (default: `ON`) - Build camera support.
 - `with_compass` (default: `ON`) - Build compass support.
 - `with_console` (default: `ON`) - Build console support.
 - `with_create` (default: `ON`) - Build iRobot Create 2 support.
 - `with_digital` (default: `ON`) - Build digital sensor support.
 - `with_graphics` (default: `ON`) - Build graphics support (requires X11 development files, such as `x11proto-dev` on Debian/Ubuntu).
 - `with_gyro` (default: `ON`) - Build gyroscope support.
 - `with_magneto` (default: `ON`) - Build magnetometer support.
 - `with_motor` (default: `ON`) - Build motor support.
 - `with_network` (default: `ON`) - Build network support.
 - `with_servo` (default: `ON`) - Build servo support.
 - `with_tello` (default: `ON`) - Build Tello support.
 - `with_thread` (default: `ON`) - Build thread support.
 - `with_time` (default: `ON`) - Build time support.
 - `with_wait_for` (default: `ON`) - Build wait_for support.
 - `with_python_binding` (default: `ON`) - Build Python binding (requires Python 3+ development files, such as `libpython3.10-dev` on Debian/Ubuntu).
 - `with_documentation` (default: `ON`) - Build documentation support (requires `doxygen` installed on system).
 - `with_tests` (default: `ON`) - Build tests.

For this example, we have decided that we don't need tello support, python bindings, documentation or tests. We can disable these modules using ```-D``` flags.

So, in order to generate the build files, we will be using this command:

```bash
cmake -Bbuild -Dwith_documentation=OFF -Dwith_tests=OFF -Dwith_python_binding=OFF -Dwith_tello=OFF
```

If any dependencies are missing, it will tell you during this step and you can install them and try again. It is probably one of the packages listed above, so it shouldn't be hard to guess what to install.

If everything is installed, the output should end with something similar to the following three lines and no errors:

```
-- Configuring done
-- Generating done
-- Build files have been written to: /home/access/kipr_installs/libkipr/build
```

At this point, all is ready to build. This will probably take more than an hour on the Pi, so you need a way to power the Wombat from wall power rather than the battery. This process will download and build additional libraries, so you need an internet connection throughout the entire time building. 

Make sure you have enough time before starting the build. When you are ready, start the compilation:

```bash
cd build
make
```

If the command exits without any errors after a few hours, the build is a success.


## Installation

The binary and headers have now been prepared, however they have not been copied to the correct system locations jet. This is not well documented in the library README.md as of writing.

To install the library you simply need to run the install target with root privileges.

```bash
# we are still in the build folder: libkipr/build
sudo make install
```

In order to use the library, you may also need to move the binary to a different folder:

```bash
sudo mv /usr/local/lib/libkipr.so /usr/lib/libkipr.so
```

## Compiling a programm

This version of the library has a different header structure than the old version, so you might need to adjust the includes. In general, associated headers are now grouped as modules. For example

```cpp
#include <kipr/servo.hpp>
```

would become 

```cpp
#include <kipr/servo/servo.hpp>
```

Headers like "utils.h" don't exist anymore, their contents have been split up into other modules. For example the "msleep()" function is now in the time module:

```cpp
#include <kipr/time/time.h>
```

To link your code, instead of using the ```-lwallaby``` compiler flag like with the old library, you now need to specify the path to the library with your build sources. If you wanted to compile the single-file project "main.cpp" to a "main" binary with the old library, you would need to do the following:

```bash
g++ -lwallaby -o main main.cpp
```

With the new library, the call would change to:

```bash
g++ -o main main.cpp /usr/lib/libkipr.so
```

Note: In theory, just specifying ```-lkipr``` should also work, but we couldn't get it to link for some reason. Also, our "hacky" solution only works when the library has been moved to ```/usr/lib/``` from the default ```/usr/local/lib/```. The latter would not work at runtime, even when specifying ```/usr/local/lib/libkipr.so``` at compile time, because just the library file name is stored in the binary, not the entire path and apparently files in ```/usr/local/lib/``` are not found at runtime.