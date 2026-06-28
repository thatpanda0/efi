# Hackintosh EFI Dump | Sequoia 15.7.3 on ThinkPad X1 Yoga-1st

### NOTE: You WILL need a USB drive with ~32GB storage, AND a second computer (with internet) to fix WiFi issues. WiFi does NOT work out of the box.

---
This repo should contain all EFI (using OpenCore 1.0.6) for booting macOS.  
It should take more or less than 40-60 seconds to boot (looking into this issue) and has support for most things:

## Working

- Audio
- Displays 
    * Comes with a LOT of color profiles
    * No HiDPI support
    * Everything else *should* work properly
- Keyboard
    * Fn key works ONLY with Fn + F[number] key
- Touchpad (and buttons)
- TrackPoint (very slow)
- *MOST* ports
    * >= USB 2.0 on all USB-A ports work
    * HDMI works
    * Headphone jack works
    * Audio buttons, power key works
    * Charging works (duh)

## Not tested / Partial

- Bluetooth support: Randomly disconnects, unpredictable during testing
- Ports (not tested): Mini DisplayPort, SD card slot

## Does not work

- Fingerprint reader (no, ThinkPads do not magically have a T2 chip)
- Continuity features
- WiFi (needs root patching)


## My WiFi doesn't work

This is expected; WiFi will NOT work out of the box because we require root patches (from OCLP).  

~~Womp womp just use ```itlwm``` instead of ```AirportItlwm```~~ (this is actually viable but HeliPort is not included here see https://openintelwireless.github.io/HeliPort/)

*Every time* you upgrade, incrementally or not (this EFI does not support macOS Tahoe yet) follow this guide:

https://github.com/randomappleboi/Native-Wifi-for-Hackintoshes-with-Intel-Wireless-cards-on-macOS-sequoia

Alternatively, check usage.md in the Post-Install Patching > WiFi and Bluetooth section. .
