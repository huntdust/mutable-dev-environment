# Vagrant environment for Mutable Instruments modules hacking

This configuration file and this shellscript create a Linux (ubuntu) virtual machine configured with all the right tools for compiling and installing the firmware of Mutable Instruments' modules.

## Kudos and inspiration

* Adafruit's [ARM Toolchain for Vagrant](https://github.com/adafruit/ARM-toolchain-vagrant)
* Novation's [LaunchPad pro](https://github.com/dvhdr/launchpad-pro)

## Requirements

* [VirtualBox 5.x](https://www.virtualbox.org/wiki/Downloads)
* [VirtualBox Extension Pack](https://www.virtualbox.org/wiki/Downloads)
* [Vagrant](https://www.vagrantup.com/downloads.html)

The Extension pack consists of a file with the `vbox-extpack` extension.  On windows, double click on it.  On OS X or Linux, the file needs to be installed from the command line with the command:

    VBoxManage extpack install <filename>

Finally if you are running a Linux operating system you will want to add your user to the `vboxusers` group so that the virtual machine can access your USB devices.  Run the following command:

    sudo usermod -a -G vboxusers $USER

Then **log out and log back in** to make sure the group change takes effect.

## Usage

To start the VM, open a terminal in the directory with the Vagrantfile and run:

    vagrant up

The first time the VM is started, all tools will be downloaded, and the latest version of the code will be grabbed from github.  The process takes about 15 minutes, depending on the speed of your internet connection or computer.

Then, you can log onto the virtual machine by running:

    vagrant ssh

Once in the virtual machine, you can try, for example, to compile Clouds' bootloader and code:

    make -f clouds/bootloader/makefile hex
    make -f clouds/makefile

To write the firmware to the module with an Olimex ARM-USB-OCD-H JTAG adapter, use:

    make -f clouds/makefile upload

Or you can generate a .wav file for the built-in audio updater:

    make -f clouds/makefile wav

The firmware update file is put in `build/clouds/clouds.wav`.

Once you're done with this hacking session, you can leave the VM terminal with:

    exit

The virtual machine continues running and can be reaccessed with `vagrant ssh`. It can also be suspended with `vagrant suspend`, halted with `vagrant halt`, and completely destroyed with `vagrant destroy`.  Note that with this last command, you might lose any files you have created inside the VM's disk image!

## Moving files between the VM and the host

By default, the working directory (`eurorack-modules`) is installed in the `/vagrant` directory, which is shared between the VM and the host.  You can thus use any text editor on the host to modify the files.  Note that any file can be transfered between the VM and the host by being copied in this location.

If you prefer working in a more self-contained environement and leave your host directory clean, you can comment the line `CODE_DIRECTORY=/vagrant` and uncomment the line `CODE_DIRECTORY=/home/vagrant` before setting up the VM.  The code will not be installed in the shared directory, and will be accessible only from within the VM.

## USB issues

To pass through USB devices from your real machine to the virtual machine, consult the [VirtualBox USB documentation](https://www.virtualbox.org/manual/ch03.html#idp96037808).

Additionally, inside the VM, you might have to run as super user (`sudo <command>`) the commands making use of a USB device because of permission issues.  A few udev rules have been added for common device (AVR ISP mkII, Olimex ARM-USB-OCD, FTDI USB-serial adapter).
