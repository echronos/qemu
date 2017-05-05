# GNU ARM Eclipse QEMU (w/ eChronos patches)

This is a fork of [GNU ARM Eclipse QEMU](http://gnuarmeclipse.github.io/qemu), which is a project intended to provide support for Cortex-M emulation in QEMU.

## Using the binaries (Tested on Ubuntu 14.04)

First, grab the dependencies you'll need:
```
sudo apt-get install libfdt-dev libsdl2-image-2.0-0 libsdl2-2.0-0
```

Note that you'll need to be on a system that has a GUI (i.e not a default Ubuntu Server installation), due to the libsdl dependency requiring a context on qemu startup.

Clone the repository and switch to the correct branch:

```
git clone https://github.com/echronos/qemu echronos-qemu
cd echronos-qemu
git checkout echronos-qemu
```
Add our qemu binary to the PATH (you can do this permanently if you want):
```
export PATH=`pwd`/path:$PATH
```
This `path` directory just contains a script that allows you to run `echronos-qemu-system-arm` anywhere on your machine.

Note, when you load a kernel with this qemu, that the path to our kernel MUST be absolute as the qemu binary must be run with the CWD in it's binary folder. If you are in the eChronos repo, this means you will want to prefix `elf` files with `` `pwd`/out/... ``.

We're ready to run a system! Say we already built an example gatria system for the stm32 (see official eChronos readme):
```
echronos-qemu-system-arm -mcu STM32F407VG -s --kernel `pwd`/out/machine-stm32f4-discovery/example/gatria-system/system

....
task b
task a
task b
b blocking
task a
task a
unblocking b
task b
task a
task b
task a
task b
task a
task b
b blocking
task a
```

Also note that in the above example we are not using the `-S` option which indicates that we will wait for a debugger to attach to the qemu machine.

## Building

To build a binary, read on.

### Prerequisites (for building)

On debian-derivatives you will need:

```
sudo apt-get install git zlib1g-dev libsdl2-dev libpixman-1-dev libsdl2-image-dev libfdt-dev
```

NOTE: Ubuntu 14.04 has a bug in it's distribution of libfdt-dev which will cause compilation to fail - see section near end of this readme for the fix.

### How to build

First, clone the repository and switch to the correct branch:

```
git clone https://github.com/echronos/qemu echronos-qemu
cd echronos-qemu
git checkout echronos-qemu
```

Now we'll configure the build. Make sure to modify the SDL2 include path below to correspond to the your SDL2 headers. (this line should be fine on debian-derivatives)
```
./configure --target-list=gnuarmeclipse-softmmu --extra-cflags="-g -I/usr/include/SDL2/ -I/usr/include/" --disable-werror
```

We're ready to build it:
```
make
```

Now copy the binaries, device support files and images to our binary distribution folder:
```
cp gnuarmeclipse/devices/*.json bin-dist
cp gnuarmeclipse/graphics/*.jpg bin-dist
cp gnuarmeclipse-softmmu/qemu-system-gnuarmeclipse bin-dist
```

And now bin-dist contains fresh binaries that can be used in the same fashion as those at the start of this readme.


### Ubuntu 14.04 `libfdt-dev` issue

Ubuntu 14.04 is distributed with an old version of libfdt-dev, so you'll need to compile it yourself. First, make sure to uninstall `libfdt-dev` if you have already installed it.

After cloning the echronos-qemu repository, you'll also need the dependencies:

```
sudo apt-get install bison flex
```

Now fetch the dtc submodule on the repository:

```
git submodule update --init dtc
cd dtc
```

Now, open up the makefile and change the `PREFIX` variable value from:

```
PREFIX = $(HOME)
```
to
```
PREFIX = /usr
```

Now we can make and install libfdt:

```
make
sudo make install
```

Now, when building qemu with this libfdt, you'll have to make a small modification to the `configure` command - instead of:

```
./configure --target-list=gnuarmeclipse-softmmu --extra-cflags="-g -I/usr/include/SDL2/ -I/usr/include/" --disable-werror
```
You need to link to the version of fdt that you just installed by adding an additional cflag `-lfdt`:

```
./configure --target-list=gnuarmeclipse-softmmu --extra-cflags="-g -I/usr/include/SDL2/ -I/usr/include/ -lfdt" --disable-werror
```
All the ordinary build instructions remain the same.
