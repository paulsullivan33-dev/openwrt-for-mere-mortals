# Choosing Hardware Honestly

**Outcome: you can judge whether a
given router is a good OpenWrt
candidate before money changes hands,
and you know the numbers that decide
it.**

This chapter is documented knowledge,
not lab work: the lab had no router.
It is also the chapter that decides
whether the rest of the guide is a
pleasant afternoon or a cautionary
tale, because OpenWrt support is
decided model by model, and sometimes
revision by revision of the same model.

## The four numbers that matter

- **Flash: 16 MB or more.** The base
  system needs room, and your packages
  need more. Devices with 4 MB of flash
  were marginal a decade ago and are
  effectively finished; 8 MB devices
  survive only in the oldest supported
  releases or with a stripped image.
  Current releases want 16 MB as a
  working minimum and are happier
  with more.
- **RAM: 128 MB or more.** 64 MB
  devices run, but the web interface
  plus any real workload will squeeze
  them. 256 MB is comfortable.
- **A supported radio.** The Wi-Fi
  chips with good open drivers are
  mostly MediaTek and Qualcomm Atheros
  designs. Broadcom radios are the
  standing disappointment: many work
  only with a limited, reverse-
  engineered driver or not at all, and
  a router that routes beautifully but
  cannot do Wi-Fi is a common way to
  waste money.
- **An exact match in the Table of
  Hardware.** OpenWrt's own database
  lists supported devices by full
  model *and hardware revision*.
  "The same name" is not enough:
  manufacturers silently change the
  chips inside a model between
  revisions, and revision C of a router
  may be supported while revision D is
  a different machine wearing the same
  plastic.

## Shapes of candidate

**A mainstream supported router** is
the easy path: a current MediaTek-
based model with gigabit ports, 256 MB
of RAM and 128 MB or more of flash
gives years of headroom and a boring
first flash. Boring is praise.

**A single-board computer** — a
Raspberry Pi with a second network
port added, or one of the small
dual-port ARM boards sold for the
purpose — makes a fine OpenWrt router
and is very hard to brick, because the
"flash" is an SD card you can rewrite
in another machine. The trade is that
routing on add-on USB network adapters
has its own performance and reliability
limits, and built-in Wi-Fi on these
boards is usually the weak kind.

**An old PC or a virtual machine** is
what the **x86/64** target is for, and
it is what this guide's lab uses: any
64-bit PC with two network ports can
run the exact image dissected in
chapter 5. As a main router it is
rarely sensible — it drinks power a
router sips — but as a way to learn
the system with zero brick risk it has
no equal, and small low-power x86
boxes with several ports are a
legitimate enthusiast choice.

**A device that is merely flashable**
— old ISP-supplied routers, anything
with 4/32 or 8/64 flash/RAM, anything
whose support page begins with opening
the case and soldering a serial header
— is a hobby project, not an upgrade.
There is no shame in hobby projects.
There is shame in mistaking one for
your household's internet connection.

## The checks before you commit

1. Find the exact model and revision
   in the Table of Hardware. If your
   revision is not listed as supported,
   stop.
2. Read the device's own page, all of
   it. It records how installation
   works on that model — most are a
   file uploaded through the stock web
   interface; some need a recovery
   mode, a serial cable, or a
   downgrade of the stock firmware
   first — and it lists what does not
   work afterwards.
3. Note the recovery story before you
   need it: most routers have a
   failsafe mode (chapter 11), and many
   have a manufacturer recovery that
   can rewrite flash over the network.
   A model with neither is a model
   where a mistake is permanent.
4. Keep the stock firmware file for
   your device downloaded and safe.
   Going back is a normal, supported
   thing to want.

One more honest point: support ends.
Releases drop devices as minimums rise,
and a router bought for OpenWrt should
be bought with the intention of
replacing it one day like any other
computer — on your schedule, not the
manufacturer's.
