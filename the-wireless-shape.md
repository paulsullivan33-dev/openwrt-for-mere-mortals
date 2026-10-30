# The Wireless Shape

**Outcome: you can describe how
wireless configuration comes to exist
on OpenWrt, why the lab's image has
none, and what the moving parts are
called when it does.**

This is the guide's most
documented-only chapter, and it says
so on purpose: the lab's target —
generic x86/64 — has no radio, the lab
had no router, and **no claim in this
chapter was proven against a real
wireless device**. What *is* proven,
from the image, is the shape of the
absence and where wireless would plug
in; the rest is the project's
documented design.

## Why there is no wireless file

Chapter 6 found no `/etc/config/wireless`
in the image. That is not an omission
in the package; it is the design.
Wireless configuration describes
specific radios — their bands, channels
and capabilities — and radios differ
per device, so the file is **generated
on the running system** by the wifi
machinery scanning the hardware it
finds (`wifi config`, run automatically
when needed), the same first-boot
pattern as `network` and `system`.

The x86/64 generic image goes further:
its package list (transcript t04)
contains the *libraries* for wireless
information (`libiwinfo`, used by LuCI
to display radios) but **no access
point daemon at all** — no `hostapd`,
no `wpad`. A PC build expects its
networking on wires; a router build
for a real model carries the wireless
stack as part of its device profile,
because on a router the radio is the
point. Moral, worth the whole
chapter: **an OpenWrt image is a
per-device artefact**, and "supports
Wi-Fi" is a property of the device
profile, not of OpenWrt in general.

## The shape, when radios exist

On a router, the generated `wireless`
file is UCI like everything else
(chapter 6's grammar), with two kinds
of section:

- **`config wifi-device`** — one per
  physical radio: band, channel,
  country code (which sets the legal
  channels and powers — set it
  correctly, it is law as well as
  physics), transmit power, and the
  driver options.
- **`config wifi-iface`** — one per
  network broadcast from a radio: the
  SSID, encryption (**WPA2 or WPA3**
  in current releases — anything older
  offered is a museum piece and should
  be refused), the key, and the
  network it attaches to, usually
  `lan`. One radio commonly carries
  two ifaces: the house network and a
  guest network, the guest one
  attached to a separate zone in the
  firewall's sense (chapter 8).

Underneath, **netifd** drives the
radio scripts, and **hostapd** (for
access point duty) and
**wpa_supplicant** (for client duty)
do the radio work; LuCI's wireless
pages are forms over the same file,
showing scan results and signal
readings gathered through **iwinfo**.

## The honest judgements

- **Driver quality is the whole
  game**, which is why chapter 2
  steered hardware choice by radio
  chipset. The configuration system
  cannot fix a radio whose open driver
  is partial: bands missing, mesh or
  client modes absent, throughput a
  fraction of stock firmware's. The
  device page says what works; believe
  it over the box copy.
- **Placement and physics outrank
  settings.** Channel width, power and
  band steering are garnish compared
  with where the router sits. OpenWrt
  gives finer control than stock
  firmware and the same walls.
- **Client mode is a first-class
  citizen**: a spare OpenWrt router
  makes a respectable wireless bridge
  or travel router (joining a hotel
  network on the radio, serving your
  own behind NAT) — a stock-firmware
  party trick it usually is not.
- **Mesh (802.11s) and fast roaming
  (802.11r)** exist in the package
  feeds and the same UCI shape;
  they are also the easiest place in
  home networking to spend a weekend.
  The guides for them start where
  this chapter ends: with a working,
  boring, single access point.

Wireless devices are also where
upgrades bite hardest in the real
world — a radio configured by a
version-specific default can come up
changed after a major release — which
is one more reason chapter 11 insists
on reading release notes before
upgrading the box the house depends
on.
