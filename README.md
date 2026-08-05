# Home Assistant support for Tuya BLE devices

## Overview

This integration supports Tuya devices connected via BLE.

It works locally over Bluetooth. The cloud is contacted once during setup, to fetch the encryption key each device needs, and after that the device is driven directly.

This repository tracks [ha-tuya-ble/ha_tuya_ble](https://github.com/ha-tuya-ble/ha_tuya_ble), the fork consolidating the active descendants of [PlusPlus-ua/ha_tuya_ble](https://github.com/PlusPlus-ua/ha_tuya_ble).

_Inspired by code of [@redphx](https://github.com/redphx/poc-tuya-ble-fingerbot)_

## Installation

Place the `custom_components` folder in your configuration directory (or add its contents to an existing `custom_components` folder). Alternatively install via [HACS](https://hacs.xyz/).

[![Open your Home Assistant instance and open a repository inside the Home Assistant Community Store.](https://my.home-assistant.io/badges/hacs_repository.svg)](https://my.home-assistant.io/redirect/hacs_repository/?owner=materko&repository=ha_tuya_ble&category=integration)

## Cloud credentials

Setup asks for an Access ID and Access Secret from a Tuya IoT cloud project, plus the login of the Smart Life app account the devices belong to. Note that the documentation of the official Tuya integration no longer describes this: that integration moved to a user code and QR flow, while Tuya BLE still authenticates against the IoT platform with a custom project.

1. Register at [iot.tuya.com](https://iot.tuya.com). This account is separate from the Smart Life app account.
2. **Cloud → Development → Create Cloud Project.** Choose Development Method **Smart Home**, Industry **Smart Home**, and a Data Center matching the region your app account was registered in. Note that Tuya offers two European data centers and they are not interchangeable: the country you pick during setup selects the endpoint, and every European country in the list maps to `openapi.tuyaeu.com`, which is **Central Europe**.
3. Under **Service API**, make sure **IoT Core**, **Authorization Token Management** and **Smart Home Basic Service** are authorized. IoT Core is free only for a trial period, which can be extended from the same page.
4. Under **Devices → Link App Account**, add your app account by scanning the QR code from the Smart Life app (*Me* → scan icon). Your devices should then be listed.
5. The Access ID and Access Secret are on the project **Overview** page.

During setup enter the country of the app account, both keys, and the app account's e-mail and password. A cloud project created before Tuya reworked its console in 2021 will reject the login with `2406 skill id invalid`; create a new one. An account registered through Google or Apple sign-in has no password to authenticate with, so set one in the app first.

## Usage

After adding to Home Assistant integration should discover all supported Bluetooth devices, or you can add discoverable devices manually.

Devices must be paired with the Smart Life app before Home Assistant can reach them. An unpaired device accepts the Bluetooth connection and then ignores the handshake, which shows up as repeated `timeout receiving response` in the log.

Bluetooth range matters more than for most integrations. Advertisements are readable at a signal strength where connections still fail; around −85 dBm or better is a reasonable target. An ESPHome Bluetooth proxy in the same room is usually the answer, and note that a proxy serves a limited number of simultaneous connections.

Entities are provided across the button, binary sensor, climate, cover, event, light, lock, number, select, sensor, switch, text and vacuum platforms, depending on the device.

## Supported devices list

* Fingerbots (category_id 'szjqr')
  + Fingerbot (product_ids 'ltak7e1p', 'y6kttvd6', 'yrnk7mnn', 'nvr2rocq', 'bnt7wajf', 'rvdceqjh', '5xhbk964'), original device, first in category, powered by CR2 battery.
  + Adaprox Fingerbot (product_id 'y6kttvd6'), built-in battery with USB type C charging.
  + Fingerbot Plus (product_ids 'blliqpsj', 'ndvkgsrm', 'yiihr7zh', 'neq16kgd', '6jcvqwh0', 'riecov42', 'h8kdwywx'), almost same as original, has sensor button for manual control.
  + CubeTouch 1s (product_id '3yqdo5yt'), built-in battery with USB type C charging.
  + CubeTouch II (product_id 'xhf790if'), built-in battery with USB type C charging.
  + Nedis SmartLife Finger Robot (product_id 'yn4x5fa7').

  All features available in Home Assistant, programming (series of actions) is implemented for Fingerbot Plus.
  For programming exposed entities 'Program' (switch), 'Repeat forever', 'Repeats count', 'Idle position' and 'Program' (text). Format of program text is: 'position\[/time\];...' where position is in percents, optional time is in seconds (zero if missing).

* Switches (category_id 'kg')
  + Fingerbot Plus (product_ids 'mknd4lci', 'riecov42', 'gnpbj0bq'), the same device family as under 'szjqr', reported by newer firmware under this category.
  + Fingerbot Touch (product_id 'bs3ubslo'), two independent touch buttons, built-in battery with USB type C charging.
  + Switch Robot (product_id '4ctjfrzq').

  Each Fingerbot Touch button has its own mode ('Click', 'Switch' or 'Program') and its own entities: a click button, a switch, touch and off-touch timing, touch enable and invert. The click button and the switch only appear while that button is in the matching mode, so pick the mode first.

* Wireless buttons (category_id 'wxkg')
  + Arlec Smart Button (product_ids 'kpzc6pm8', 'ja5osu5g').

* Smart Locks (category_id 'ms')
  + Smart Lock (product_ids 'ludzroix', 'isk2p555', 'gumrixyt', 'uamrw6h3', 'sidhzylo', 'mqc2hevy', 'a6nttc41', '7a4xvbtt').
  + TEKXDD Fingerprint Smart Lock (product_id 'okkyfgfs').
  + Fingerprint Smart Lock (product_id 'k53ok3u9').
  + Primebras Athenas Lock (product_id '6fibxtph').
  + Foxgard Smart Fingerprint Door Lock (product_id '99gv5nmz').
  + BSTUOKEY Invisible induction lock (product_id 'kpn4zaf7').
  + Guard Dog Security Smart Lock (product_id 'wgv4haro').

* Smart Locks, second generation (category_id 'jtmspro')
  + Drawer Lock CTL20H (product_id 'y2yaegze').
  + A1 Ultra-JM (product_id 'hc7n0urm').
  + HU06 Smart Lock (product_id 'stugc8dl').
  + Raycube K7 Pro+ (product_id 'xicdxood').
  + LA-01 Smart lock (product_id 'oyqux5vv').
  + A1 PRO MAX (product_id 'rlyxv7pe').
  + CentralAcesso (product_id 'ebd5e0uauqx0vfsp').
  + B16 (product_id 'ajk32biq').
  + Smart Cylinder Lock (product_ids 'z7lj676i', 'hs21i377').
  + Smart Lock (product_id 'kholoaew').
  + CS-9 Smart Fingerprint Lock (product_id 'pyawczjj').
  + Gainsborough Liberty BLE Lock GGC01HA (product_id 'yfqp0shy').
  + XCase NX-4964 Lock Box (product_id 'qicggi0m').

* Temperature and humidity sensors (category_id 'wsdcg')
  + Soil moisture sensor (product_id 'ojzlzzsw').
  + Soil Thermo-Hygrometer (product_id 'tv6peegl').
  + Temperature Humidity Sensor (product_ids 'iv7hudlj', 'jm6iasmb', 'vlzqwckk', 'tr0kabuq', 'vyfoip9h', '1jvidcsf').
  + Temperature Humidity Sensor SS302 (product_id '6lbesej0').

* Soil moisture sensors (category_id 'zwjcy')
  + SRB-PM01 Soil Moisture Sensor (product_id 'jabotj1z').

* CO2 sensors (category_id 'co2bj')
  + CO2 Detector (product_id '59s19z5m').

* Climate (category_id 'wk')
  + Thermostatic Radiator Valve (product_ids 'drlajpqc', 'nhj2j7su', 'zmachryv').

* Climate (category_id 'wkf')
  + Thermostatic Radiator Valve (product_id 'llflaywg').

* Smart water bottle (category_id 'znhsb')
  + Smart water bottle (product_id 'cdlandip').

* Water valves and irrigation (category_id 'sfkzq')
  + Aldi/Ferrex Smart Water Valve (product_id '16wgjvck').
  + Irrigation computer (product_ids '6pahkcau', 'hfgdqhho', 'qycalacn', 'fnlw6npo', 'jjqi2syk').
  + Valve controller (product_ids 'svhikeyq', '0axr5s0b', 'e1poaiwa', 'd4vpmigg').
  + Water valve controller (product_ids 'nxquc5lb', '46zia2nz', '1fcnd8xk').
  + ZX-7378 Smart Irrigation Controller (product_id 'ldcdnigc').
  + Unistyle WT-04W Water Timer (product_id 'ojrvmfkk').
  + HCT-611 Water Timer (product_id 'tqzkwarw').

* Irrigation computer (category_id 'ggq')
  + Irrigation computer (product_ids '6pahkcau', 'hfgdqhho').
  + YZD02B dual irrigation timer (product_id 'jntxv3q4').

* Water meters (category_id 'slj')
  + RESTMO BT Water Meter (product_id 'mqqna0px').

* Lights (category_id 'dd')
  + LGB102 Magic Strip Lights (product_id 'nvfrtxlq').
  + Magiacous Floor Lamp (product_id 'umzu0c2y').
  + Comfamoli Sunset Lamp (product_id '6jxcdae1').
  + RGB Strip Light (product_id '0qgrjxum').

  Other devices in this category are set up with a generic light profile.

* Light bulbs (category_id 'dj')
  + LED BULB B509Z2 (product_id 'bpqbwf8y').

* Curtains and blinds (category_id 'cl')
  + Blind Controller (product_ids '4pbr8eig', 'vlwf3ud6').
  + Curtain Controller (product_id 'kcy0x4pi').
  + AOK AM24 Venetian Blinds Motor (product_id 'dy4dh1q0').
  + AOK AM25 Roller Blinds Motor (product_id 'v3fzfd2y').

* Window cleaners (category_id 'cxjmb')
  + Window Cleaner Robot (product_id 'pnxl0r3l').

* Aroma diffusers (category_id 'jsq')
  + Dituo DT-T2190A Aroma Diffuser (product_id 'if1nolcm').

* Batteries (category_id 'dcb')
  + PARKSIDE Smart battery 4Ah (product_id 'z5ztlw3k').
  + PARKSIDE Smart battery 8Ah (product_id 'ajrhf1aj').

## Support project

The following is from [@PlusPlus-ua](https://github.com/PlusPlus-ua), who wrote the original integration.

I am working on this integration in Ukraine. Our country was subjected to brutal aggression by Russia. The war still continues. The capital of Ukraine - Kyiv, where I live, and many other cities and villages are constantly under threat of rocket attacks. Our air defense forces are doing wonders, but they also need support. So if you want to help the development of this integration, donate some money and I will spend it to support our air defense.
<br><br>
<p align="center">
  <a href="https://www.buymeacoffee.com/3PaK6lXr4l"><img src="https://www.buymeacoffee.com/assets/img/custom_images/orange_img.png" alt="Buy me an air defense"></a>
</p>
