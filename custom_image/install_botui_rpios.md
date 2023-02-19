# Installing BotUI on PRiOS

This section will guide you through installing BotUI on a generic Raspberry Pi OS (Lite) installation.

## Requirements

 - A Wombat with a modern version of 32-Bit Raspberry Pi OS installed on the SD card (ideally [configured to work with the Wombat screen](rpios_installation.md))
 - Network and Internet access on the Wombat (e.g. using ethernet on the local network)
 - git and CMake (install using ```sudo apt install git cmake```)
 - [Legacy libwallaby](install_libwallaby_rpios.md) installed

## Workspace setup 

To get started, ssh into your Wombat and create/navigate to a folder you wish to use for installation. Then clone the [frenchbakery/botui](https://github.com/frenchbakery/botui) repository because it has some patches that allow compilation using a modern compiler (more on that below):

```bash
mkdir -p kipr_installs
cd kipr_installs
git clone https://github.com/frenchbakery/botui
cd botui
```

Recent versions of RPiOS don't support Qt4 anymore, so we have to use Qt5. 

At the time of writing, the master branch doesn't quite support Qt5 jet and wouldn't compile with a modern compiler, so you will have to use the qt5-modern-compatability branch ([commit db5733e9dba82749a28b41f17b7f753d4d87aa0f](https://github.com/frenchbakery/botui/commit/db5733e9dba82749a28b41f17b7f753d4d87aa0f) at the time of writing):

```bash
git switch qt5-modern-compatability
git pull
```

## Dependencies

Before you can build the library, you need to install some dependencies. These are not documented on the official repository README.md jet. We need a feq Qt5 packages as well as OpenSSL:

```bash
sudo apt install libssl-dev qttools5-dev qtbase5-dev
# you may also need these packages as well if CMake tells you (not quite sure if these are necessary for the qt5-fixed branch)
sudo apt install qtdeclarative5-dev qtquickcontrols2-5-dev
```

BotUI also needs a few other KIPR libraries and programs that need to be installed manually in the following order.

### libkar

libkar needs Qt5 base which is already installed from before. To install it, simply clone the repository and use the master branch ([commit 8731c42116f784b39e84650c898d69e66c1711fc](https://github.com/kipr/libkar/commit/8731c42116f784b39e84650c898d69e66c1711fc) at the time of writing).

```bash
cd kipr_installs
git clone https://github.com/kipr/libkar
cd libkar
```

Create a build directory, initialize cmake, run make and and make install.

```bash
mkdir build
cd build
cmake ..
make
sudo make install
```

### pcompiler

pcompiler also depends on Qt5 base as well as on libkar, so it needs to be installed after libkar. Thi installation itself is quite similar.

Clone the reqpository and use the master branch ([commit e78d6197075870adb16475933985bdf9f23f33fb](https://github.com/kipr/pcompiler/commit/e78d6197075870adb16475933985bdf9f23f33fb) at the time of writing).

```bash
cd kipr_installs
git clone https://github.com/kipr/pcompiler
cd pcompiler
```

Create a build directory, initialize cmake, run make and and make install.

```bash
mkdir build
cd build
cmake ..
make
sudo make install
```


## Compiling

In modern versions of GCC, apparently the vtable creation has changed, as there are several missing vtable errors when compiling the KIPR code. Therefore, we have created a patch, that adds dummy functions or virtual specifiers to generate these vtables. vtable explaination: https://stackoverflow.com/questions/3065154/undefined-reference-to-vtable

Now we can compile the project as normal. Create a build directory, generate the build files using CMake and start the build using make:

```bash
mkdir build
cd build
cmake ..
make
```

This takes a few minutes and may occasionally spit out some warnings about deprecated Qt functions but that shouldn't be an issue. It should exit with no errors.

## Installation

The binary and headers have now been prepared, however they still have to be copied to the correct system locations.

To install BotUI you simply need to run the install target with root privileges.

```bash
# we are still in the build folder: libwallaby/build
sudo make install
```

Now, botui can be started from anywhere using the ```botui``` command. However, this will probably fail because it will not find the pcompiler shared object. We have to create a link in ```/usr/lib`` for it to be found:

```bash
sudo ln -s /usr/local/lib/libpcompiler.so /usr/lib/libpcompiler.so
```

## Custom display setup

If you are using Raspberry Pi OS Lite, you will probably encounter the following error:

```
qt.qpa.xcb: could not connect to display 
qt.qpa.plugin: Could not load the Qt platform plugin "xcb" in "" even though it was found.
This application failed to start because no Qt platform plugin could be initialized. Reinstalling the application may fix this problem.

Available platform plugins are: eglfs, linuxfb, minimal, minimalegl, offscreen, vnc, xcb.

Aborted
```

This is because there is no display server and window manager running on the Pi. We will install the X11 display server with the simple matchbox window manager in this example. The matchbox window manager does only support one window at a time and is therefor ideal for kiosk applications like this one.

To get started, install all the dependencies:

```bash
sudo apt install matchbox xorg x11-xserver-utils xinit ttf-mscorefonts-installer xwit
```

Then open the ```/etc/X11/xinit/xinitrc``` file, comment out any existing, uncommented lines and add the following content at the end:

```bash
# custom setup for single view application
matchbox-window-manager & #-use_titlebar no & # start matchbox window manager
# start botui
botui
```

This will start BotUI whenever X is started (like with the ```startx``` command).

We can also add that to our ```.profile``` to automatically start on the console on login:

```bash
# start the UI
startx &
```

To start this automatically on boot, we have to enable auto login using ```sudo raspi-config``` (System Options->Bott/AutoLogin->ConsoleAutoLogin).

