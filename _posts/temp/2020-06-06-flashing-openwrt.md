---
layout: post
title: Flashing OpenWRT on an Archer C20 AC750 V5
tags: [ router, openwrt, learning, notes ]
---
* * *
Trying to fix a non-responsive router by flashing OpenWRT


[OpenWRT](https://openwrt.org/) is basically linux on your router. I'm sure
you'd rather have linux there, instead of whatever junk firmware the hardware
maker has installed.

Installing it is usually fairly simple, except when you run into edge cases with
devices that are on the periphery of support.

We happened to order the Archer C20 AC750 V5, and it didn't start up the default
wifi on connecting.
I immediately checked for
OpenWRT support. Having found a few [promising](https://forum.openwrt.org/t/porting-firmware-to-tp-link-archer-c20-v5/35512/113) [leads] online, I decided to go ahead with it,


### Status

| Link                                                                                                           | What did it do                                         |
|----------------------------------------------------------------------------------------------------------------|--------------------------------------------------------|
| [Official image](https://www.tp-link.com/in/support/download/archer-c20/#Firmware)                             | Was downloaded, 30s later restart loop                 |
| [Official image, EU version](https://www.tp-link.com/in/support/download/archer-c20/#Firmware)                 | Was downloaded, 30s later restart loop                 |
| [OpenWRT 19.07.03](https://github.com/ashegoulding/openwrt/releases/tag/v19.07.3-tl_archer_c20v5) - recovery   | ditto                                                  |
| [OpenWRT 19.07.02](https://github.com/ashegoulding/openwrt/releases/tag/v19.07.2-tl_archer_c20v5) - recovery   | Was downloaded, 30s later restart loop                 |
| [OpenWRT 18.06.05](https://forum.openwrt.org/t/porting-firmware-to-tp-link-archer-c20-v5/35512/114) - recovery | Downloaded, power button on and unblinking, others off |
| [Linaro's OpenWRT link](https://github.com/Linaro1985/openwrt/releases/download/191121/TPLinkArcherC20V5.zip)  | Same as above                                          |
