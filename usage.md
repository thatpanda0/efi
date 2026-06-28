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

You will then want to follow the first half of the UnPlugged guide. However, ***IGNORE*** the "Notes for Sonoma and Later" part of the README.md in corpnewt/UnPlugged. 

Attempting to use an older ```com.apple.recovery.boot``` in the OPENCORE folder will cause a kernel panic instantly, due to "wrong" WiFi kexts (presumably). 



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

## Post-Install patching
### Mounting EFI

**Prerequisites**
- Python installer for macOS (find it online)
- MountEFI (explained below)

Install Python. Check that it works by running ```python3``` in Terminal. 

**Actual guide**

***Read, carefully, before doing any action on your laptop. It is very easy to screw this up.***

Before attempting to fix WiFi, we must first mount our EFI to separate our USB. This can be done using https://github.com/corpnewt/MountEFI. 

Run it using ``./MountEFI.command`` and select disk0s1. Your EFI should be mounted. To check, run 

```
diskutil mount disk0s1
```

in Terminal, and check that the EFI partition is mounted properly. It should look like

```
EFI Partition:
    EFI (folder)
        BOOT (folder)
        OC (folder)
```

It is imperative that **the EFI partition needs to have an EFI folder inside.** Otherwise, your Hackintosh will be unable to read the disk and boot. 

<details>
  <summary>I didn't read the guide fully | I didn't check my EFI was configured properly | MountEFI didn't work</summary>
  Hopefully you didn't wipe your USB drive. Boot from your USB drive again, and boot into whatever your disk is called. 
</details>


### Getting WiFi and Bluetooth to work
---

Prerequisites:
- OpenCore Legacy Patcher app (https://github.com/dortania/OpenCore-Legacy-Patcher/releases)
- ProperTree (optional) (https://github.com/corpnewt/propertree)

Install OCLP. Make sure ProperTree works. 

After restarting, run ```sudo diskutil mount disk0s1``` again. Open your EFI folder to the ```config.plist``` file. Use TextEdit (or Propertree, if it is installed) to open config.plist.

Locate the following:

```
...
<key>DeviceProperties</key>
<dict>
    <key>Add</key>
    <dict>
        <key>#PciRoot(0x0)/Pci(0x1C,0x2)/Pci(0x0,0x0)</key>
        <dict>
            ...
        </dict>
...
```

Remove the ``#`` preceding ``#PciRoot(0x0)/...``. Reboot.

Open OCLP. Click the Root Patches button. You should get the "Modern Wireless" root patch; patch it. Reboot (again). 

*Again*, mount your EFI and navigate to your plist file. Remove the comment you made. If you want Bluetooth to work, add the following in ``NVRAM > Add > 7C436110-AB2A-4BBB-A880-FE41995C9F82``:

**Using TextEdit (macOS)**

```
...
<key>bluetoothExternalDongleFailed</key>
<data>AA==</data>
<key>bluetoothInternalControllerInfo</key>
<data>AAAAAAAAAAAAAAAAAAA=</data>
...
```

**Using ProperTree (python3)**

Add a new ``Data`` object with name ``bluetoothExternalDongleFailed`` and value ``00``.  
Add another new ``Data`` object with name ``bluetoothInternalControllerInfo`` and value ``00000000 00000000 00000000 0000`` (28 zeros). 

Finally, for hopefully the last time, reboot. 

## Blocking OTA updates

For now, any online guide should work fine; go to System Settings > General > Software Update > (i) button > turn off Automatic Downloads. 

This will ensure no kernel modifications are done, since Apple stages the next macOS version even when 'only' downloading. 

If you don't, you may have a longer boot time and having WiFi broken; fix it by doing the same thing you did previously. 
