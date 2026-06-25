# Usage

This guide assumes you have a USB drive with at least 32.0 GB of storage, and that you are to wipe ***all*** content on the drive you wish to install it on. 

## Formatting the USB Drive (Windows)

We will format the USB drive in the same way as corpnewt/UnPlugged on GitHub. To do this, we can use the disk management tool (```diskmgmt.msc```), or diskpart:

```
diskpart
list disk
select disk X
clean
create partition primary size=1024
format fs=fat32 quick label=OPENCORE
assign
create partition primary
format fs=exfat quick label=UnPlugged
assign
exit
```

Where ```X``` refers to your USB drive (should be found with ```list disk```).

You will then want to follow the first half of the UnPlugged guide. However, ***IGNORE*** the "Notes for Sonoma and Later" part of the README.md in corpnewt/UnPlugged. Attempting to use an older ```com.apple.recovery.boot``` in the OPENCORE folder will cause a kernel panic instantly, due to "wrong" WiFi kexts (presumably). 



- Use macrecovery.py to download the ```com.apple.recovery.boot``` folder for macOS 15
- Copy the folder.
- Open File Explorer, and paste said folder into ```OPENCORE```.
- Paste the *contents* of the same folder (``com.apple.recovery.boot``), into ```UNPLUGGED```. 
<br>

- Download the BOOT and OC folders from this repository, and make a EFI folder, also in ``OPENCORE``. 
- Move BOOT and OC into the EFI folder.
<br>

- Download macOS Sequoia 15.7.3 using ```gibMacOS.py```. 
- Download ``UnPlugged.command`` from https://github.com/corpnewt/UnPlugged/blob/main/UnPlugged.command
- Move your downloaded files into the ```UnPlugged``` partition of the USB. 



Your drive should now look like this:

```
USB drive
    - OPENCORE
        -- EFI
            BOOT (from this repo)
            OC (from this repo)
        -- com.apple.recovery.boot (macOS 15)

    - UnPlugged
        -- (Files downloaded from gibMacOS.py)
        -- com.apple.recovery.boot
```

---

## Booting into macOS and installing

Now, you'll want to actually boot into macOS recovery. This should go well, though expect the boot time to be up to 20 minutes (I have an average of ~7-10 minute boot time, though once macOS actually installs, it should be closer to 40 seconds).

This can be done via pressing ```ENTER``` while your laptop is booting, and select ``F12`` for temporary boot device. Choose your USB (obviously). 

You should then see the GUI to boot into UNPLUGGED. Select it (obviously). The icon should look like a Settings icon (though yellow?). 

Open Disk Utility to check the disk number of your USB drive. You may need to click the "View" button to change the view and see all drives (including offline/unmounted ones). 

***You will now wipe your disk you are to install macOS on. Save any and all locally stored files; you will not be getting them back.***

Select the disk you are to install macOS on, and click the "Erase" button, to format it as APFS file system, using GUID Partition Table (GPT). 

Make sure you know which disks and which partitions correspond to the OPENCORE and UnPlugged partitions of your connected USB drive. 

Open Terminal via Utilities (top bar) > Terminal. Type in (to mount your UNPLUGGED and OPENCORE partitions):

```
# Optional: diskutil list physical
mkdir /Volumes/UnPlugged
/sbin/mount_exfat /dev/diskXsY /Volumes/UnPlugged
```
For your Disk X, partition Y your UnPlugged is in. Repeat for OPENCORE. 
Now that your disks are mounted, run ```./UnPlugged.command```. 

Follow the instructions, you may be greeted with an error about installing macOS Sequoia 15.4.1, being unable to automatically detect the version. Ignore it.

Your drive should show up as a possible installation media. Install to it. 

---

## First Boot / Post-install

Your WiFi will not work. This was alluded to before in README.md. 
You will need to additionally install OpenCore Legacy Patcher (OCLP), for root patches and continue with the guide as in README.md.

After that, everything *should* work. Add issues to this GitHub page if anything doesn't. 
