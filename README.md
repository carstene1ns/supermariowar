# Super Mario War

[![Travis][travis-img]][travis-link] [![AppVeyor][appveyor-img]][appveyor-link] [![Freenode channel][freenode-img]][freenode-link]

- [About](#about)
- [Building](#building-instructions)
	- [Get the code](#get-the-code)
	- [Requirements](#requirements)
	- [Linux](#linux)
	- [Mac OSX](#mac-osx)
	- [Windows](#windows)
	- [ARM / Raspberry Pi](#arm-devices)
	- [Other devices](#other-devices)
	- [Android](#android)
	- [Asm.js](#asmjs)
	- [Build configuration](#build-configuration)
- [How to play](#how-to-play)


## About

Super Mario War is a Super Mario multiplayer game. The goal is to stomp as many other Mario's as possible to win the game. There is a range of different gameplay modes in the game, like Capture-The-Flag, King of The Hill, Deathmatch, Team Deathmatch, Tournament Mode, Collect The Coins, Race, and many more. The game also includes a level editor which lets you create your own maps from scratch, or re-create sections from your favorite Mario game, your imagination is the limit! Recently included is a world editor, which strings a bunch of levels with specified conditions to create an SMB3-like experience merged with tournament like play. The game is more importantly a tribute to Nintendo and the original fangame game Mario War by Samuele Poletto.

The game uses artwork and sounds from Nintendo games. We hope that this noncommercial fangame qualifies as fair use work. We just wanted to create this game to show how much we adore Nintendo's characters and games.

### Features

- Up to four players deathmatch fun
- a whole bunch of game modes (featuring GetTheChicken, Domination, CTF, ...)
- online and local multiplayer
- Comes with the leveleditor - you can create your own maps...
- ... and a lot of people did so. There are currently over 1000 maps
- More fun than poking a monkey with a stick
- The whole source code of the game is available, for free
- uses SDL and is fully portable to windows, linux, mac, ...
- CPU Players
- will make you happy and gives you a fuzzy feeling

### Supported platforms

- Linux
- Windows
- Mac OS X
- ARM devices (eg. Raspberry Pi)
- XBox (?)
- Android (experimental)
- asm.js (experimental)


## Building instructions

### Requirements

- C++11 supporting compiler (eg. gcc-4.8)
- CMake (>= 2.6)
- SDL (1.2 or 2.0), with
    - SDL_image
    - SDL_mixer
- yaml-cpp (included)
- ENet (optional, included)

On Debian-based systems, the following command installs the required tools:

    apt-get install cmake libsdl1.2-dev libsdl-image1.2-dev libsdl-mixer1.2-dev

On systems using RPM: `yum install cmake SDL-devel SDL_image-devel SDL_mixer-devel`

On Arch and derivatives: `pacman -S cmake sdl sdl_image sdl_mixer`

For other systems, you can download the development files manually from:

- http://www.cmake.org/cmake/resources/software.html#latest
- http://www.libsdl.org/download-1.2.php
- http://www.libsdl.org/projects/SDL_image/release-1.2.html
- http://www.libsdl.org/projects/SDL_mixer/release-1.2.html

### Get the code

This repository contains some submodules which you can use if the dependencies are not available for your OS, are outdated or you simply don't want to install them on your system. To use the included libraries, do a recursive cloning:

`git clone --recursive https://github.com/mmatyas/supermariowar.git`

Alternatively, you can also initialize the submodules manually:

```sh
git clone https://github.com/mmatyas/supermariowar.git
cd supermariowar
git submodule update --init
```

If you'd rather use the system libraries, please see the [Build configuration](#build-configuration) section for disabling this feature.

### Linux

Create a build directory and run CMake there to configure the project. Then simply call `make` every time you want to build. The binaries will be generated in `./Build/Binaries/Release` by default. In short:

```sh
unzip data.zip
mkdir build && cd build
cmake ..
make -j4 # -jN = build on N threads
./Binaries/Release/smw ../data
```

The main build targets for `make` are:

- smw
- smw-leveleditor
- smw-worldeditor
- smw-server

If you prefer to work within an IDE (CodeBlocks, Eclipse, ...), you can also generate project files for it using CMake. You can find more information in [Build configuration](#build-configuration).

To create installable packages, simply run `make package`. This will create TGZ, DEB and RPM archives.

### Mac OSX

You'll probably need Xcode and its command line tools; you can install SDL and CMake manually from its site, or you can get them with Homebrew: `brew install cmake sdl2 sdl2_image sdl2_mixer`. Then follow the Linux instructions to build SMW.

### Windows

If you're using MinGW Shell/MSYS or Cygwin, you can follow the Linux guide. You can also generate a project file with CMake for various IDEs, such as CodeBlocks, Eclipse or Viual Studio.

Visual Studio: *TODO: check this section*

- make sure you have CMake in your PATH
- go to Build directory and issue `cmake ..`
- open the generated solution smw.sln, right-click on the smw project and go to Configuration Properties -> C/C++ - Command line and remove everything there
- on the same group, go to Linker and add your lib directory there
- change also system to WINDOWS (not console)
- now it will compile and smw.exe can be found in build\Binaries\Debug\smw.exe. make sure you copy the necessary SDL dlls along with smw.exe, otherwise it won't work

### ARM devices

You can build SMW on various ARM devices, like the Raspberry Pi, following the Linux instructions. Some boards may require different compiler flags hovewer, in this case you might want to manually edit `cmake/PlatformArm.cmake`.

If you know how to do it, you can also cross-compile the usual way, by setting up a toolchain or emulating your device. In this case, see the [Other devices](#other-devices) section. If your processor is not detected correctly (eg. in a rootfs), you can override it by adding the ARM_OVERRIDE_ARCH flag to CMake, eg. `-DARM_OVERRIDE_ARCH=armv6`.

For example, here is how to set up a simple build environment for the Raspbery Pi on Ubuntu 14.04:

```sh
sudo apt-get install qemu-user-static debootstrap

# Set up a Raspbian rootfs in the 'raspberry' directory
sudo qemu-debootstrap --arch armhf wheezy raspberry http://archive.raspbian.org/raspbian

# Mount some file systems in the rootfs
sudo mount -o bind /dev  raspberry/dev
sudo mount -o bind /proc raspberry/proc
sudo mount -o bind /sys  raspberry/sys
sudo cp /etc/resolv.conf raspberry/etc/resolv.conf

# Enter
sudo chroot raspberry /bin/bash
```

Then:

```sh
echo "deb http://archive.raspbian.org/raspbian jessie main" >> /etc/apt/sources.list
wget http://archive.raspbian.org/raspbian.public.key -O - | apt-key add -
apt-get update
apt-get install build-essential g++ git \
    cmake libsdl1.2-dev libsdl-image1.2-dev libsdl-mixer1.2-dev
```

Now you can continue with cloning the repository (eg. in `/root`) and following the Linux instructions. If you want to build for the first-gen Raspberry, you might have to call CMake as `cmake .. -DARM_OVERRIDE_ARCH=armv6l` to avoid linking errors. For other devices, you might need to edit the compiler flags as described above.

### Other devices

You should be able to port SMW to any device where SDL (either 1.2 or 2.0) works. Generally, this involves the following steps:

- get the cross compiler toolchain of your device
- cross-compile the SDL libs, if they are not included
  - build SDL
  - build SDL_image with at least PNG support
    - requires zlib
    - requires libpng
  - build SDL_mixer with at least OGG support
    - requires libogg
    - requires libvorbis
- create a CMake toolchain file and define your compiler and paths
- build using the toolchain file

### Android

The Android port uses a different build system, you can find more details [here](https://github.com/mmatyas/supermariowar-android).

### ASM.JS

*TODO: Update this part, it's broken with the latest emscripten*

SMW can be build to run in your browser. For this, you need
Emscripten with the special LLVM backend, and Clang.
See [here](https://kripken.github.io/emscripten-site/docs/building_from_source/LLVM-Backend.html) for more information.

You can prepare a build directory with the following commands:

```sh
$ mkdir build-js && cd build-js
$ ln -s ../data data
$ emconfigure cmake .. -DTARGET_EMSCRIPTEN=1 -DNO_NETWORK=1
```

Then build with:

```sh
$ emmake make
$ BUILDPARAMS="-O3 -v --preload-file data -s OUTLINING_LIMIT=60000 -s ALLOW_MEMORY_GROWTH=1 -s ASSERTIONS=1"
$ emcc Binaries/Release/smw.bc -o smw.html $BUILDPARAMS
$ emcc Binaries/Release/smw-leveledit.bc -o leveledit.html $BUILDPARAMS
$ emcc Binaries/Release/smw-worldedit.bc -o worldedit.html $BUILDPARAMS
```

### Build configuration

*TODO: expand this section*

You can change the build configuration by setting various CMake flags. The simplest way to do this is by running `cmake-gui ..` from the `Build` directory. You can read a short description of an element by hovering the mouse on its name too.

Alternatively, you can pass these options directly to CMake as `-DFLAGNAME=VALUE` (eg. `cmake .. -DUSE_SDL2_LIBS=1`).



## How to play

Please see documentation in the docs/ directory.


[travis-img]: https://travis-ci.org/mmatyas/supermariowar.svg?branch=master
[travis-link]: https://travis-ci.org/mmatyas/supermariowar
[appveyor-img]: https://ci.appveyor.com/api/projects/status/github/mmatyas/supermariowar?svg=true
[appveyor-link]: https://ci.appveyor.com/project/mmatyas/supermariowar
[freenode-img]: http://img.shields.io/freenode/%23supermariowar.png
[freenode-link]: https://webchat.freenode.net/?channels=supermariowar
