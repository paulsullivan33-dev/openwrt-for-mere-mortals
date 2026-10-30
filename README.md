# OpenWrt for Mere Mortals

OpenWrt is a Linux distribution small
and stubborn enough to run the router:
a real operating system — kernel,
package manager, firewall, web
interface — in the space stock firmware
uses for a settings page. This guide
explains it the way the series explains
everything: from a real artefact,
taken apart on a table, with nothing
claimed that was not looked at.

The artefact is the official
**OpenWrt 25.12.6** image for x86/64,
downloaded from the project's own
server and verified against its
published SHA-256 before a single file
inside it was trusted. The lab then
did three things with it, all recorded
as transcripts in `openwrt-lab/`:

1. **Dissected it.** Partitions,
   bootloader, kernel, the compressed
   root filesystem — and inside, the
   actual configuration the system
   ships: the firewall file complete,
   the DHCP defaults, the package
   database of 182 installed packages,
   and the discovery that two of the
   most-discussed files in OpenWrt,
   `network` and `system`, are not in
   the image at all, because they are
   generated on first boot by code that
   ships in the image and can be read.
2. **Rebuilt it to order.** The
   official ImageBuilder produced a
   custom image with an editor and a
   process viewer baked in. Its
   manifest proves the packages are
   there, its unpacked rootfs shows
   the binaries, and the attempt also
   produced the guide's favourite
   honest finding: a default
   ImageBuilder build is *not* the
   official image — the whole LuCI web
   stack is something the release
   builders add, and now you know how
   to add it back.
3. **Booted it.** The stock image ran
   in an emulator (no router, no real
   network), generated its
   configuration, brought up its LAN at
   192.168.1.1, and answered real `uci`
   commands — closing the loop between
   what the files promised and what
   the system does.

One version note, because it dates
almost everything written before it:
**25.12 replaced opkg with apk** as
the package manager. The guide is
written for the system as it is now.

## What this guide covers

- **What OpenWrt Is** — a distribution,
  not a jailbreak; its history in one
  story; what "support" means.
- **Choosing Hardware Honestly** —
  the four numbers, the radio-chipset
  question, and the checklist before
  money moves.
- **The Moving Parts** — image, split
  root, procd, UCI, netifd, apk, LuCI:
  the cast, in order of boot.
- **Getting the Firmware** — reading
  image filenames, the checksum habit,
  and the factory/sysupgrade fork.
- **Anatomy of a Real Image** — the
  lab's download on the table:
  partitions, GRUB, kernel, rootfs.
- **UCI — the Configuration System** —
  the grammar, the files that ship,
  the files that are generated, and a
  running system's answers.
- **Packages with apk** — the 182
  stock packages, the feeds, and the
  opkg transition stated plainly.
- **Network and Firewall Shape** —
  the two-sided machine and its
  default answer to the world.
- **The Wireless Shape** — why the
  image has no wireless file, and
  what exists when radios do.
- **Building Your Own Image** — the
  ImageBuilder, a failed first attempt,
  two builds, and the manifest diffs.
- **Upgrading and Recovery** —
  sysupgrade, attended sysupgrade,
  failsafe, and the recovery ladder.
- **Recipes** — eight small jobs, from
  setting the root password to the
  pre-upgrade ritual.
- **The Neighbours** — stock firmware,
  DD-WRT, the x86 firewalls, and the
  full build system.
- **Limits** — what the system cannot
  do and what this guide could not
  prove, in one chapter on purpose.
- **Troubleshooting** — the dozen
  failures people actually meet.
- **Glossary** — every term, defined
  in the guide's own words.

## What it doesn't cover

Flashing a real router (no hardware
was harmed, or used, in this lab);
Wi-Fi behaviour on real radios; LuCI
as a clicked-through interface; and
the full source build system, which
chapter 13 points at and this guide
deliberately stays downstream of.
Each absence is labelled where it
occurs and gathered in `criteria.md`.

## How to use this guide

Read chapters 1–3 for the shape,
chapter 5 with a downloaded image of
your own for the pleasure of
recognition, and chapter 6 twice —
everything after it is UCI with
different nouns. Keep chapter 15
bookmarked for the day something
behaves. The transcripts in
`openwrt-lab/transcripts/` (t00–t13)
are the lab's raw evidence; every
factual claim about the image traces
to one of them.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Networking for Mere Mortals](https://paulsullivan33-dev.github.io/networking-for-mere-mortals/)
- [Home Networking for Mere Mortals](https://paulsullivan33-dev.github.io/home-networking-for-mere-mortals/)
- [DNS for Mere Mortals](https://paulsullivan33-dev.github.io/dns-for-mere-mortals/)
- [Linux for Mere Mortals](https://paulsullivan33-dev.github.io/linux-for-mere-mortals/)
- [Virtual Machines for Mere Mortals](https://paulsullivan33-dev.github.io/virtual-machines-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/openwrt-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/openwrt-for-mere-mortals/blob/main/LICENSE-CODE).
