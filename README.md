# usb-boot
Running Raspbian from USB Devices : Made Easy

Created by RonR, extraxted from https://www.raspberrypi.org/forums/viewtopic.php?f=29&t=196778


NOTE: usb-boot as been updated to allow the use of direct USB booting on the Raspberry Pi 4 (in addition to hybrid SD/USB booting on all Raspberry Pi models). usb-boot also allows Raspberry Pi 4 users to migrate their SD card based systems to a USB device.

A recurring topic of discussion is how to configure and reliably run Raspbian on a USB flash drive, USB hard drive, or USB SSD instead of an SD card.

A Raspberry Pi 3B+ has a native USB boot mode (this mode has to be manually enabled by setting an OTP bit on a Raspberry Pi 3B). This native USB boot mode has serious compatibility issues. A bootcode.bin file is available for older Raspberry Pi models. Unfortunately, both of these approaches have serious limitations and once working, can easily be broken by simply plugging in an additional USB storage device.

The easiest and most reliable way to run Raspbian on a USB device with any Raspberry Pi is to leave an SD card containing Raspbian in place, but use it only for starting Raspbian that is residing on a USB device. While setting up such a configuration is not rocket science, it can be confusing to a newcomer or someone unfamiliar with Linux internals. In an effort to simplify the task, I've created the attached script named 'usb-boot' to automate the process.

If usb-boot is running on a Raspberry Pi 4, usb-boot first prompts: 'Use SD card to boot the USB device?'

If 'No' is selected, the SD card will not be altered and the direct USB boot capability of the Raspberry Pi 4 will be used.

[NOTE: The direct USB boot capability of the Raspberry Pi 4 requires an updated bootloader and firmware: Raspberry Pi EEPROM Manager]

If usb-boot is running on a Raspberry Pi 3B+ or a Raspberry Pi 3B with its OTP bit set, usb-boot first prompts: 'Use SD card to boot the USB device (recommended)?'

If 'No' is selected, the SD card will not be altered, but booting the USB device may be limited and/or unreliable as described above.

usb-boot then presents a list of available USB mass storage devices and prompts: 'Select the USB mass storage device to boot'

Use the arrow keys on your keyboard to navigate to the desired device and press the spacebar to select it. Then use the tab key to navigate to the 'Ok' or 'Cancel' button and press the return key.

usb-boot will then prompt: 'Replicate BOOT/ROOT contents from /dev/mmcblk0 to /dev/sdX?'

/dev/mmcblk0 is the SD card and /dev/sdX is the USB device.

Select 'No' if the USB device already has Raspbian on it and you wish to use it (nothing will be copied).

Select 'Yes' if you want to copy the Raspbian on your SD card to the USB device (everything will be copied).

If you select 'Yes', usb-boot will then prompt: 'Select the partition table type to use (MBR = 2TB Maximum)'

usb-boot will then prompt: 'All existing data on USB device /dev/sdX will be destroyed!' and ask: 'Do you wish to continue?'

If you select 'Yes', the copy will begin. The time required for this process will depend on the amount of data on your SD card and the speed of your storage devices.

usb-boot will then complete the configuration process and warn you of any potential conflicts it detects.

When usb-boot has finished, you should be able to reboot and be running Raspbian on the USB device (first power off and remove the SD card if not using the SD card to boot the USB device).

=====

sdc-boot provides a convenient way to select which attached device will be booted.

Usage syntax is:

sdc-boot [ /dev/sdX2 | /dev/mmcblk0p2 | hhhhhhhh-02 | hhhhhhhh-hhhh-hhhh-hhhh-hhhhhhhhhhhh ]

/dev/sdX2 is a USB device

/dev/mmcblk0p2 is the SD card

hhhhhhhh-02 | hhhhhhhh-hhhh-hhhh-hhhh-hhhhhhhhhhhh is a device identified by its PARTUUID

If no device is specified, the currently selected boot device will be displayed.

=====

GPT partition tables are necessary for devices whose size is over 2TB.

mbr2gpt converts an MBR partition table on a USB device to a GPT partition table, as well as optionally expanding the ROOT partition and enabling booting via an SD card.
mbr2gpt converts any size USB device, including SD cards placed in a USB adapter.

!!! DO NOT PROCEED UNLESS YOU HAVE A RELIABLE BACKUP OF THE DEVICE BEING CONVERTED !!!
!!! INITIAL TESTING SHOULD BE PERFORMED ON A USB DEVICE CONTAINING EXPENDABLE DATA !!!

Usage syntax is:

mbr2gpt /dev/sdX

mbr2gpt will prompt for permission to perform the following optional functions:

1. Convert the USB device to use GPT partition tables

2. Expand the ROOT partition to use all available space

3. Set the SD card to boot the USB device

=====

set-partuuid displays or changes the ROOT partition PARTUUID on a device.

Usage syntax is:

set-partuuid device [ hhhhhhhh-02 | hhhhhhhh-hhhh-hhhh-hhhh-hhhhhhhhhhhh | random ]

device may be /dev/sdX2 or /dev/mmcblk0p2

If no partuuid is specified, the current ROOT partition PARTUUID will be displayed.
