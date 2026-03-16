# Hackintosh EFI Dump | Sequoia 15.7.3 on ThinkPad X1 Yoga-4th

### NOTE: You WILL need a USB drive with ~32GB storage, AND a second computer (with internet) to fix WiFi issues. WiFi does NOT work out of the box.

---
This repo should contain all EFI (using OpenCore 1.0.6) for booting macOS.  
It should take more or less than 40 seconds to boot (looking into this issue) and has support for everything with a few caveats:

## Working

- Audio
- Displays 
    * Comes with a LOT of color profiles, choose your favorite
    * No HiDPI support if I believe
    * Everything else *should* work properly
- Keyboard
    * Fn key works ONLY with Fn + F[number] key
- Touchpad (and buttons)
- TrackPoint
- *MOST* ports
    * Last time I checked, support for at least USB 2.0 on all USB-A ports work
    * HDMI works
    * Headphone jack works
    * Audio buttons, power key works
    * Charging works (duh)

## Not tested

- Bluetooth support
- Ports:
    * Mini DisplayPort, SD card slot

## Does not work

- Fingerprint reader (no, ThinkPads do not magically have a T2 chip)
- Continuity features
- WiFi* (VERY BIG ASTERISK!! see bottom)





## My WiFi doesn't work

This is expected; WiFi will NOT work out of the box because we require root patches (from OCLP).  


~~Womp womp just use ```itlwm``` instead of ```AirportItlwm```~~ (this is actually viable but HeliPort is not included here see https://openintelwireless.github.io/HeliPort/)

*Every time* you upgrade, incrementally or not (this EFI does not support macOS Tahoe yet) follow this guide:

https://github.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia

If you're too lazy to click a link,
(yes I used Gemini 3.1 to write the TLDR flame me now)

### **Preparation**
*   **Downloads needed:** Hackintool, OpenCore Legacy Patcher (OCLP), ProperTree (or any ```plist``` editor), and kexts (though should be included in EFI) (`IOSkywalkFamily`, `IO80211FamilyLegacy`, `AirPortBrcmNIC`, `AMFIPass`, and specifically **`AirportItlwm_v2.3.0_stable_Ventura`**).

### **Step 1: Configuration**
1.  **Find Device Path:** Open Hackintool, go to PCIe, find your Intel Wireless card, right-click, and copy the Device Path.
2.  **Spoof as Broadcom:** In your `config.plist` under `DeviceProperties > Add`, paste the Device Path and add the provided properties (from the guide's table) to spoof the card as a Broadcom BCM4360 adapter.
3.  **Add Kexts:** Add the downloaded kexts to your EFI and config.plist in this **exact order** (top to bottom):
    *   `IOSkywalkFamily.kext`
    *   `IO80211FamilyLegacy.kext`
    *   `AirPortBrcmNIC.kext` *(Plugin of IO80211FamilyLegacy)*
    *   `AMFIPass.kext`
    *   `AirportItlwm.kext`
4.  **Block Apple Kext:** In `Kernel > Block`, enable the `Allow IOSkywalk Downgrade` patch.
5.  **Lower SIP:** Go to `NVRAM > 7C436110...` and set `csr-active-config` to `03080000`.
6.  **Reboot.**

### **Step 2: OCLP Patching**
1.  **Apply Patch:** Open OpenCore Legacy Patcher, select **Post-Install Root Patch**, and start patching. Reboot when finished. *(Reset NVRAM if you hit SIP errors).*
2.  **Disable the Spoof:** Open your `config.plist`, go back to `DeviceProperties > Add`, and comment out the Broadcom spoof by putting a `#` in front of your device path (e.g., `#PciRoot(...)`).
3.  **Reboot** (again). At this point, all WiFi and most Continuity features should work. 

### **Step 3: Fixing Bluetooth**
1.  **Add NVRAM Keys:** Go to `NVRAM > 7C436110...` in your `config.plist` and add two new Data keys:
    *   `bluetoothExternalDongleFailed` = `00`
    *   `bluetoothInternalControllerInfo` = `0000000000000000000000000000`
2.  **Save and Reboot.** If Bluetooth fails, reset NVRAM a few times.
