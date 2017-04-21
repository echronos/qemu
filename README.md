# GNU ARM Eclipse QEMU (w/ eChronos patches)

This is a fork of [GNU ARM Eclipse QEMU](http://gnuarmeclipse.github.io/qemu), which is a project intended to provide support for Cortex-M emulation in QEMU.

## Installing binary release

(Coming soon...)

## Building

To build a binary, read on.

### Prerequisites

This fork requires the same prerequisites as the GNU ARM Eclipse QEMU fork. However, the build process for the GNU ARM Eclipse QEMU fork is rather involved, and many systems will already have most of the prerequisites. It is for this reason that I recommend attempting to build it using these build instructions and just install prerequisites as the build fails rather than following through the entire docker-image process as would normally be required (i.e those on the GNU ARM Eclipse QEMU pages).

### How to build

First, clone the repository:

```
git clone https://github.com/echronos/qemu echronos-qemu
cd echronos-qemu
```

Now we'll configure the build. Make sure to modify the SDL2 include path below to correspond to the your SDL2 headers. (you'll need both the SDL2 and SDL2_Image packages installed)
```
./configure --target-list=gnuarmeclipse-softmmu --extra-cflags="-g -I/usr/include/SDL2/ -I/usr/include/" --disable-werror
```

We're ready to build it:
```
make
```

Now copy the device support files and images to a place where the binary can see them:
```
cp gnuarmeclipse/devices/*.json gnuarmeclipse-softmmu
cp gnuarmeclipse/graphics/*.jpg gnuarmeclipse-softmmu
```

Add our qemu binary to the PATH (you can do this permanently if you want):
```
export PATH=`pwd`/path:$PATH
```
This `path` directory just contains a script that allows you to run `echronos-qemu-system-arm` anywhere on your machine.

And we're ready to run a system. Say we already built an example gatria system for the stm32 (see official eChronos readme):
```
echronos-qemu-system-arm -mcu STM32F407VG -s --kernel <my_echronos_repo>/out/machine-stm32f4-discovery/example/gatria-system/system

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
