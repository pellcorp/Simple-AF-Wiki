# Firmware Flashing the Cartographer3D

These instructions are for the Cartographer3D Probe V3 - USB version only. *DO NOT ATTEMPT THIS IF THIS IS NOT THE VERSION YOU HAVE!*

The Cartographer3d Probe V3 has its own firmware developed by the Cartographer3d developers, and it is important to make sure the Cartographer3d is on the latest supported firmware to function properly with the Simple AF firmware installed on the printer. Below are the steps to help you flash your Cartographer to the recommended firmware version.
Always check via discord for which version Simple AF requires.

!!! DANGER

    Failing to follow these instructions can **BRICK** your probe!
    ***We can not stress enough how important it is to head all warnings because the Simple AF developers will not be responsible for damages to your hardware.***

!!! warning
    Corrupting the Katapult boot loader is possible using a Linux VM to flash the probe ***It is strongly recommended*** to use a live USB over a VM.

## Verify your Cable

Ensure the cable you are using is pinned correctly. For both flat-pack and right-angle cartographers the default cable that comes with your probe may not be pinned correctly. Refer to the image below.

![image](docs/assets/images/carto_connector.png)

!!! Warning
    The flat-pack and right-angle pin-out shown in the image is different from the low-profile Cartographer's pin-out!

    **DO NOT** not use the ``firmware.cartographer3d.com``to flash your probe, it **WILL NOT** flash the proper version of the `CARTOGRAPHER 5.1.0` firmware for the K1 and K1 Max!

## Linux Environment

You will need a linux environment. This can be a Raspberry Pi, desktop Linux or even a Linux Server as long as you can access a USB port. In addition, you can also flash from a bootable Live USB key running Ubuntu 24.04 Desktop edition as long as you have an available  USB port for the probe to plug into using the included USB cable.

!!! warning
    We do not recommend trying to flash your probe through a Linux VM due to reports of improperly flashed probes, to do so is oncea gain at your own risk. There is an incompatibility with pyserial and MIPS that prevents you from being able to do this from the printer. An issue for this has been opened with <https://github.com/Arksine/katapult/issues/137>.

## Creating a Live USB Key

If you need to create a live USB with Ubuntu 24.04 Desktop, make sure the USB key drive is at least 8GB in size! You can follow the provide link for a how to create one. <https://ubuntu.com/tutorials/try-ubuntu-before-you-install#1-getting-started>

You are now ready to follow the steps below to flash your Cartographer3D probe.

### 1. Installation Dependencies

On your Linux device run the following commands to install the essential packages to flash your probe.

        sudo apt-get update
        sudo apt-get install virtualenv python3-dev python3-pip libffi-dev build-essential git dfu-util

### 2. Cloning the Klipper Environment and Cartographer-Klipper

Once the installation dependencies are complete run the following commands to install Klipper and Cartographer-Klipper

        git clone "https://github.com/Klipper3d/klipper" $HOME/klipper
        git clone "https://github.com/Cartographer3D/cartographer-klipper.git" $HOME/cartographer-klipper```

### 3. Setup Klipper Virtual Env

Run the following command to set the Klipper Virtual Environment

        virtualenv --system-site-packages $HOME/klippy-env
        $HOME/klippy-env/bin/pip3 install -r $HOME/klipper/scripts/klippy-requirements.txt
    
### 4. Cartographer3d V5.1.0 Firmware

==**This firmware is provided by RichardTHF from Cartographer3d.com specifically for the K1, K1C and K1 Max. It is critical that you flash your probe with this version of the Probe V3 firmware to avoid stuttering during bed meshes.**==

!!! WARNING
    You must flash your probe even if you received it new with firmware already installed.

### 5. Connect Cartographer via USB

Plug the probe into your Pi, Linux desktop/server or computer running Linux from USB key using usb cable that came with your probe. Check that is connected by typing `lsusb` You should see an entry something like this:

`Bus 001 Device 067: ID 1d50:614e OpenMoko, Inc.`

### 6. Enable Bootloader Mode

Once the probe is connected it is time to enable the Bootloader, run the command below.

        CARTO_DEV=$(ls /dev/serial/by-id/usb-Cartographer*)
        cd $HOME/klipper/scripts
        sudo $HOME/klippy-env/bin/python -c "import flash_usb as u; u.enter_bootloader('$CARTO_DEV')"

!!! Info
    **IMPORTANT:** If you get a message like `ls: cannot access '/dev/serial/by-id/usb-Cartographer': No such file or directory`, it means you forgot the `*` in the command above. If you still get this message after correcting the command it means your probe's cable is pinned incorrectly.

Once bootloader is enabled you should see a message like:

`Entering bootloader on /dev/serial/by-id/usb-Cartographer_614e_16000C000F43304253373820-if00`

!!! Info
    If the carto does not enter bootloader mode, it is possible you forgot to use `sudo`!
    If your carto does show up in '/dev/serial' but won't enter bootloader mode, you will need to fix this with [DFU mode](#flashing-k1-firmware-via-dfu-mode)

### 7. Flashing

Enter the following command:

        CATAPULT_DEV=$(ls /dev/serial/by-id/usb-katapult*)
        sudo $HOME/klippy-env/bin/python $HOME/klipper/lib/canboot/flash_can.py -f $HOME/cartographer-klipper/firmware/v2-v3/survey/5.1.0/Survey_Cartographer_K1_USB_8kib_offset.bin -d $CATAPULT_DEV

You should see the following out output:

    Attempting to connect to bootloader
    CanBoot Connected
    Protocol Version: 1.0.0
    Block Size: 64 bytes
    Application Start: 0x8002000
    MCU type: stm32f042x6
    Flashing '/home/ubuntu/cartographer-klipper/firmware/v2-v3/survey/5.1.0/Survey_Cartographer_K1_USB_8kib_offset.bin'...

    [##################################################]

    Write complete: 22 pages
    Verifying (block count = 338)...

    [##################################################]

    Verification Complete: SHA = BB45B9575AC57FFF03CA5FE09186DB479E09BF53
    CAN Flash Success

**Note:** If the carto does not flash, it is possible you forgot to use `sudo`!

When you reconnect your carto to your printer it should show a version `CARTOGRAPHER K1 5.1.0`:

![image](assets/images/cartographer_k1_510.png)

## Flashing K1 Firmware via DFU Mode

You will need to temporarily switch to the beta branch to get the 5.1.0 combined dfu firmware:

        cd $HOME/cartographer-klipper
        git fetch
        git switch beta
        git reset --hard origin/beta

You need to bridge the boot pins before you plug your carto in via USB to your Linux session, make sure `lsusb` reports it being in DFU mode, it should show

![image](assets/images/carto_lsusb_dfu.png)

Then cd to the combined firmware directory

        cd $HOME/cartographer-klipper/firmware/v2-v3/combined-firmware/5.1.0

And run dfu-util to write the firmware:

        sudo dfu-util -R -a 0 -s 0x08000000:leave -D Full_Survey_Cartographer_CrealityK1_USB_5_1_0.bin

![image](assets/images/carto_dfu.png)

**Source:** <https://docs.cartographer3d.com/cartographer-probe/firmware/manual-methods/cartographer-with-input-shaper/update-via-dfu-mode>
