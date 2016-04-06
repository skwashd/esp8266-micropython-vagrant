# ESP8266 & MicroPython SDK Virtual Machine
Vagrant file to build a virtual machine that can compile the
[ESP8266 open SDK](https://github.com/pfalcon/esp-open-sdk) &amp; 
[MicroPython](https://micropython.org/) firmware.

Note that MicroPython support for the ESP8266 is in early stages and does
not support the full capabilities of other MicroPython boards.  However this VM
will help make it easy to build and install MicroPython for the ESP8266 to test
it out and even contribute to it.

Many thanks to the contributors of ESP open SDK & MicroPython for making
their excellent software available!

# Dependencies

You must have the following software installed:

*  [VirtualBox](https://www.virtualbox.org/)
*  [Vagrant](https://www.vagrantup.com/)

# Usage

Clone this repository and navigate to it in a command terminal, then run the
following command to bring the Vagrant virtual machine up and provision it for
compiling the tools:

    vagrant up

After the virtual machine is brought up and provisioned use the following
command to enter an SSH session on it:

    vagrant ssh

Once inside the virtual machine you will see two git repositories that have
already been cloned:

*   [esp-open-sdk](https://github.com/pfalcon/esp-open-sdk) - This is an SDK to
    compile code for the ESP8266's processor.

*   [micropython](https://github.com/micropython/micropython) - This is the MicroPython
    SDK which allows running embedded Python code on an ESP8266.

## ESP Open SDK

The ESP Open SDK was compiled during the provisioning of the Vagrant box. If you need
to recompile it, execute the following commands:

    cd ~/esp-open-sdk
    make STANDALONE=y

Note that if the compilation fails with an error like:

    [ERROR]    collect2: error: ld terminated with signal 9 [Killed]
    [ERROR]    make[4]: *** [cc1] Error 1
    [ERROR]    make[3]: *** [all-gcc] Error 2

This means the virtual machine ran out of memory during the last stages of the
compilation process.  You can resolve this by bumping up the memory available to
the VM by changing this line in the Vagrantfile

    # Bump the memory allocated to the VM up to 1 gigabyte as the compilation of
    # the esp-open-sdk tools requires more memory to complete.
    v.memory = 1024

I found at least 1 gigabyte of memory was required to compile the SDK (and that
is the default configuration value).  If you change the Vagrantfile you will
need to stop and restart the VM (see the Stopping & Starting the VM section
further below).

## MicroPython

MicroPython has been built during the box provisioning.

If you make any changes to MicroPython, like modifying the ./scripts/main.py
file to change the boards's behavior on boot, you can recompile the MicroPython
firmware by running these commands again:

    cd ~/micropython/esp8266
    make


## Flashing ESP8266 Firmware

MicroPython was compiled during the provisioning of the virtual machine, but
you need to run the final step to prepare the firmware and flash it onto your
device.

Any time you make changes to your python code, such as main.py, you will need
to run the flash command below.

To flash a new version of your firmware onto your device run the following
command:

make PORT=/dev/ttyUSB0 deploy


## Stopping & Starting the VM

To stop the VM make sure you've exited from any SSH session on it (run the `exit` 
command) and then run this command inside the directory with the Vagrantfile:

    vagrant halt

To start the VM and SSH into it again just run:

    vagrant up
    vagrant ssh

## Credits
This Vagrant project extends the [Adafruit ESP8266 MicroPython Vagrant
project](https://github.com/adafruit/esp8266-micropython-vagrant).
[Tony DiCola](https://github.com/tdicola)'s work made it very easy for me to
add the extra steps to fully automate all the build steps when provisioning a
new VM.
