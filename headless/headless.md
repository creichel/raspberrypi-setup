# Headless mode on Raspberry Pi

## 1. Install Raspbian (Lite)

Download it from official Raspberry Pi website:
https://www.raspberrypi.org/downloads/

Connect your microSD card and copy the image to it via Etcher:
https://www.balena.io/etcher/

Reconnect the card again. We have to do some stuff ;)

## 2. Activate SSH
Make an empty file called `ssh` without any file extension and save it into the root of the `boot` partition of your microSD card.

## 3. Auto-connect with WiFi
Make a new file, called `wpa_supplicant.conf`. Copy and edit the following wifi setup template:
```
country={YOUR ISO COUNTRY CODE}
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1

network={
    ssid="{YOUR WIFI NAME}"
    scan_ssid=1
    psk="{YOUR WIFI PASSWORD}"
}
```
Everything in `{}` (including `{}`) should be edited by you. You can find the country code of your country here: https://en.wikipedia.org/wiki/ISO_3166-1

# Links
- Raspberry Pi Documentation - Headless setup: https://www.raspberrypi.org/documentation/configuration/wireless/headless.md
