# ESPHome-Config-for-localbytes-plug-pm
In this repo is an alternative to the [Official Localbytes Config](https://github.com/LocalBytes/esphome-localbytes-plug) for the [LocalBytes Power Monitoring Smart Plug](https://www.mylocalbytes.com/products/smart-plug-pm).

## Disclaimer
I am not affiliated with LocalBytes in any way.

You are flashing a firmware onto your device which is not from the manufacturer. You should be careful when doing things like this and check where the firmware comes from and what it does.

## Why do this?
The official firmware has a specific feature set and has not seen much in the way of changes over time as ESPHome has itself become more featureful and optimised.

The official firmware can also be a little slow to update which in some cases has left people unable to install or update their devices.

I am producing this as an alternate option with a slightly different feature set and kept more up to date with the offerings of ESPHome.

This also reduced my device count in the ESPHome builder tool which want to be individually compiled and uploaded to devices witth fixed credentials. Instead I can keep them more as a "Just Works" Product.

## The Device
The Smart Plug itself runs a ESP8266 family chip (Specifically a ESP8285) with 1MB of onboard flash to work with.

It comes out the box with either ESPHome or Tasmota Firmware as selected at time of purchase. There is no hardware difference between them and they can be flashed back and forth as needed.

## Features
- Wi-Fi Provisioning.
- IPv6 Support.
- Full compatibility with Home Assistant without any other setup.
- Fully Functional WebUI to allow for direct control outside of Home Assistant.
- Power on default state can be set as:
    - Always Off
    - Always On
    - Restore Power Off State
- Device status diagnostic sensors.
- Factory Reset by power cycling 7 times within 10 seconds of each cycle.
- WebUI has the Ability to upload firmware to it directly in `.bin` or `.bin.gz` format. NB: The firmware must be able to fit into the free space available. The exact limit will vary depending on version but will likely be around the 480-500K mark.

    WARNING: If flashing to Tasmota from ESPHome then you should flash the `tasmota-lite` image. DO NOT flash `tasmota-minimal` from ESPHome as it will BRICK THE DEVICE.

## Wishlist Features that are not implemented
### Home Assistant integegrated auto update system.
This is a really useful feature but the hardware limits of this device make implementing it impossible at this stage, However if some optimisations come into ESPHome in the future then it is something I would like to revisit. However for now precompiled updates can be installed by uploading them to the WebUI.

### Button's to flash to alternate firmwares directly.
For the same reasons as the update system this is not possible. However as with updates, It is possible to upload other firmwares to the WebUI as long as they fit into the space limitations.

## Install Instructions
### From a Plug running ESPHome Already
- Connect the plug to your Wi-Fi network if you have not done so already.
- You need to "Take Control" of the device using the ESPHome Builder Tool (explaining setting this up is out of scope for this repo). Which will leave you will a configuration file that looks something like this:
```
substitutions:
  name: localbytes-plug-pm-123456
  friendly_name: Localbytes Plug PM 123456
packages:
  localbytes.plug-pm: github://LocalBytes/esphome-localbytes-plug/localbytes-plug-pm.yaml@main
esphome:
  name: ${name}
  name_add_mac_suffix: false
  friendly_name: ${friendly_name}
api:
  encryption:    
    key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

You Should replace the package section so it looks like this:

```
substitutions:
  name: localbytes-plug-pm-123456
  friendly_name: Localbytes Plug PM 123456
packages:
  MichaelMKKelly.localbytes-plug-pm: github://MichaelMKKelly/ESPHome-Config-for-localbytes-plug-pm/localbytes-plug-pm.yaml@main
esphome:
  name: ${name}
  name_add_mac_suffix: false
  friendly_name: ${friendly_name}
api:
  encryption:    
    key: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

- OPTIONAL: Once you have flashed the device in this manner if you prefer to not use the ESPHome builder tool any longer, you can delete the device from the builder tool then use the WebUI to upload the latest precompiled version of the firmware from the releases section of this repo.

Note: You may have to "Reconfigure" the device in Home Assistant during this process if you adding/removing/changing encryption keys.

### From a Plug running Tasmota
- Connect the plug to your Wi-Fi network if you have not done so already.
- Access the Tasmota WebUI and select "Firmware Upgrade".
- We now want to move to "Tasmota Minimal" to ensure there is room on the bulb to be able to flash our new firmware.
- in the "Upgrade by web server" section you should see the following URL:
  `http://ota.tasmota.com/tasmota/release/tasmota.bin.gz`

  You should change this by adding `-minimal` to the file name so it should now be:

  `http://ota.tasmota.com/tasmota/release/tasmota-minimal.bin.gz`

  Then press "Start Upgrade".
- After a few moments you will be greated by the a notice saying "MINIMAL firmware please upgrade". Again you should select "Firmware Upgrade".
- Now you should upload the latest precomppiled binary of the Firmware from the releases section of this repo. By selecting the file and pressing "Start Upgrade"
- After a moment the Smartplug will restart and you will now neeed to reconnect it to your Wi-Fi network (This time as a ESPHome Device).
- It should now be discovered in Home Assistant (if not then you may need to manully add it by IP Address)

## Updating to a New Version
You can just upload the new firmware binary from this repo to the WebUI. This in most cases should deploy the update and bring the device straight back up, however it is possible that some updates may require you to reconnect the device to your Wi-Fi Network. Please keep this in mind if you are updating devices that you are not nearby to.

## Credits
- LocalBytes - For making/whitelabeling/selling the [Smartplug](https://www.mylocalbytes.com/products/smart-plug-pm), and publishing the [Official Config](https://github.com/LocalBytes/esphome-localbytes-plug).
- ESPHome Team and the Open Home Foundation - For making [ESPHome](https://esphome.io/).
- JamesSwift - For their work with their [Orginal Repo](https://github.com/JamesSwift/localbytes-plug-pm) which was forked to make the official firmwre.