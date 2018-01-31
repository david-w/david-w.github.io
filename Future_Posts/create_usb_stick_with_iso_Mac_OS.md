This is how I use a usb stick (aka flash-drive) to behave like a bootable CD.  This is not the way to create a bootable usb stick, for installing a live OS.  That is a different procedure altogether.  This is an easy method to make a stick to install Linux on a PC, but I also include the process to convert the .iso to a .dmg for installation on a Mac.
I'm using a Mac to create the stick, but the steps to do this on a Linux box are similar.


1. Download the iso file you wish to use.

 ###### **Note: The following command is only used if you are going to use the usb stick to install an OS on a Mac. Mac OS uses .dmg files and this step converts the .iso to a .dmg file.  Then simply substitute the converted .dmg in step 6.**
        hdiutil convert /path/to/downloaded.iso -format UDRW -o /path/to/converted.dmg2  

2. Run
       diskutil list
   Note which devices are present.

3. Insert usb drive.

4. Run
       diskutil list
   again. This time note the additional device. Example:(/dev/disk2)

5. Unmount the usb drive using the device name from step 4.
       diskutil unmountDisk /dev/disk2

6. Run
       sudo dd if=/path/to/downloaded.iso of=/dev/disk2 bs=1m
   ###### **Make sure you are writing to the correct device before hitting return! dd is powerful, especially with sudo and will blow away data without warning.**

7. Finally, eject the usb stick.
       diskutil eject /dev/disk2

That's it.....easy!
