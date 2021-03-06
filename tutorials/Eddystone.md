<!--- Copyright (c) 2016 Gordon Williams, Pur3 Ltd. See the file LICENSE for copying permission. -->
Eddystone Beacons
=================

<span style="color:red">:warning: **Please view the correctly rendered version of this page at https://www.espruino.com/Eddystone. Links, lists, videos, search, and other features will not work correctly when viewed on GitHub** :warning:</span>

* KEYWORDS: Module,Modules,BLE,Bluetooth,EddyStone,FatBeacon,iBeacon,Beacon,Physical Web
* USES: Puck.js,BLE,Only BLE

**Note:** For iBeacons, see [this page](/Puck.js iBeacon)

[Eddystone](https://github.com/google/eddystone) is an open beacon format from Google
that allows you to transmit a URL that will appear as a notification on
a user's phone.

Android phones > 4.4 will have support - but it may need enabling.
[See here](https://developers.google.com/beacons/) for more information.

**Note:** [Google has now disabled Eddystone notifications](https://android-developers.googleblog.com/2018/10/discontinuing-support-for-android.html)
because of abuse by advertisers, so Eddystone devices will no longer appear as Android Notifications.

All you need to do to use it is use the [[ble_eddystone.js]] module:

```
require("ble_eddystone").advertise("goo.gl/B3J0Oc");
```

From then on, Espruino will broadcast the URL to anything that will listen. It
is helpful if you use a descriptive title on the web page you're using, as this
will appear in the notification area.

**Note:** Since you can't advertise while you're connected via Bluetooth LE,
you will have to disconnect from your Puck for it to start transmitting.

While Espruino can transmit pretty much anything, your phone will
only notify you for certain URLs.

The URLs:

* Must be HTTPS URLs
* Must be [less than or equal to 17 characters long](https://github.com/google/eddystone/tree/master/eddystone-url)

Realistically the easiest way to do this is to use [Goo.gl](https://goo.gl/) as a URL shortener. You can still use
`#` to pass data - for instance `https://goo.gl/D8sjLK#42`.

While you only have 17 characters, `https://`, `www.`, `.com`, `.org`, `.edu`, `.net`, `.info`, `.biz` and `.gov`
in URLs are automatically shortened. So for example `require("ble_eddystone").advertise("https://www.esprino.com/Puck.js")` is
still fine, even if it would appear to be far too long.

**To turn Eddystone advertising off** simply call `NRF.setAdvertising({});`

**Note:** While advertising Eddystone, Espruino will not advertise its own name, so will not be connectable.


An Example
==========

* Go to the [Meme Generator website](https://imgflip.com/memegenerator)
* Generate a suitable image and copy the (https) URL
* Go to the Bitly](https://bitly.com/) URL shortener
* Create a shortened URL and then copy it into a `require("ble_eddystone").advertise("https://bit.ly/abcdef");` command
* Once executed (and you have disconnected), Espruino will start advertising Eddystone
* You can also call  `NRF.setAdvertising({});` to stop advertising


Advanced
--------


**`require("ble_eddystone").advertise` will overwrite
Espruino's advertising (name, services, etc) with the Eddystone packet.**

You can use `require("ble_eddystone").get` with the same options as
`advertise` to get the array of advertising data to use, which you can
feed directly into `NRF.setAdvertising()`, allowing you to set other options
such as advertising rate.

In Espruino 1v92 and later you can also supply an array of advertising data
to make Espruino send each advertising packet in turn.

For Eddystone and normal Espruino connection info:

```
NRF.setAdvertising([
  require("ble_eddystone").get(...),
  {} // this will add a 'normal' advertising packet showing name/etc  
  ], {interval:100});
```

Or for Eddystone, iBeacon, and to report battery level:

```
NRF.setAdvertising([
  require("ble_ibeacon").get(...),
  require("ble_eddystone").get(...),
  { 0x180F : [95] }
  ], {interval:100});
```

It is also possible to add Eddystone in addition to Espruino's existing advertising
by setting the Eddystone advertising inside the Advertising Scan Response:

```
NRF.setScanResponse(require("ble_eddystone").get("goo.gl/B3J0Oc"));
```

EddyStone UUID
--------------

Eddystone URLs are just one type of Eddystone advertising - you can also use UUIDs using `ble_eddystone_uid` - you just need to supply `namespace` and `instance` arrays as shown on https://github.com/google/eddystone/tree/master/eddystone-uid :

```
NRF.setAdvertising([
  require("ble_eddystone_uid").get([0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00], // namespace
                                   [0x00, 0x00, 0x00, 0x00, 0x00, 0x00]), // instance
  {} // this will add a 'normal' advertising packet showing name/etc
], {interval:100});

// or

require("ble_eddystone_uid").advertise(
        [0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00, 0x00],  // namespace
        [0x00, 0x00, 0x00, 0x00, 0x00, 0x00]); // instance
```
