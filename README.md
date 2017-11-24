# QEMU (for eChronos RTOS quickstart & regression tests)

This is a fork of [QEMU](http://github.com/qemu/qemu).

## Using the binaries (Tested on Ubuntu 14.04)

First, grab the dependencies you'll need:

    sudo apt-get install libpixman-1-0 libglib2.0-0

Now fetch the latest binary release from this repository (see 'releases' tab)

    wget 'https://github.com/echronos/qemu/releases/download/v2.11.0-rc2/qemu-system-arm_2.11.0-rc2_trusty.deb'

To install it:

    sudo dpkg -i qemu-system-arm_2.11.0-rc2_trusty.deb

We're ready to run a system! (for more info see official eChronos readme):

    cd echronos
    prj/app/prj.py build machine-qemu-simple.example.gatria-system

    # Run the generated system in qemu (press `ctrl-c` to close QEMU after it is finished)
    qemu-system-arm -M lm3s6965evb -nographic -semihosting -S -s --kernel out/machine-qemu-simple/example/gatria-system/system

    # To connect and view debug output run gdb in another shell prompt
    arm-none-eabi-gdb -ex "target remote :1234" out/machine-qemu-simple/example/gatria-system/system
    (gdb) b fn_a
    Breakpoint 1 at 0x800065c: file packages/rtos-example/gatria-test.c, line 41.
    (gdb) c
    Continuing.

    Breakpoint 1, fn_a () at packages/rtos-example/gatria-test.c:41
    41	{
    (gdb) n
    43	    rtos_unblock(0);
    (gdb) n
    44	    rtos_unblock(1);
    (gdb) c
    Continuing.
    task a -- lock
    task b -- try lock
    task a -- unlock
    ...

## Building

To build a binary, follow the 'ordinary' QEMU build instructions in `README_OLD`, and then use the standard debian packaging mechanism to put the binary into an installable package. Alternatively, just run the binary as-is.
