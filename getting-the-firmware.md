# Getting the Firmware

**Outcome: you can find the right image
for a device, say what the file names
mean, and prove a download is exactly
the file the project published.**

Everything comes from the project's own
site, **downloads.openwrt.org**, under
`releases/`. There are three kinds of
thing there, and confusing them is the
first classic mistake:

- **Stable releases** — numbered by
  year and month (25.12), with point
  releases as fixes accumulate
  (25.12.1, 25.12.2, and so on). This
  is what almost everyone should run.
- **Release candidates** — previews of
  the next stable, for testers.
- **Snapshots** — built every day from
  the development tree. Powerful,
  current, and unsupported in the
  specific sense that yesterday's is
  gone forever and packages for it
  vanish just as fast. Not a first
  install.

Inside a release, the tree is
`targets/<target>/<subtarget>/`: the
x86 family is target `x86`, the 64-bit
PC build is subtarget `64`. A router
is usually target and model together —
its device page gives the exact path,
because the file names are long and
the cost of a near-miss is a brick.

## Reading an image file name

The lab's file, taken apart:

```
openwrt-25.12.6-x86-64-generic-squashfs-combined.img.gz
|       |       |   |  |       |         |
project version target sub-   device   root file-  layout
                        target profile  system    style
```

- **squashfs or ext4** — the two root
  filesystem styles. squashfs (with the
  overlay from chapter 3) is the normal
  choice and the only sensible one on
  small flash. The ext4 variant exists
  mainly for disks and virtual machines
  where a plain writable filesystem is
  more convenient; it gives up the
  clean reset-to-defaults that the
  overlay provides.
- **combined** — a whole-disk image:
  bootloader, kernel partition and root
  partition in one file, made to be
  written to a disk, card or flash in
  one go. Router targets instead offer
  files named for their flash layout,
  typically a `sysupgrade` image (for
  a device already running OpenWrt) and
  a `factory` image (for one still
  running stock firmware). **Factory
  versus sysupgrade is the second
  classic mistake**: they are not
  interchangeable, and the device page
  says which you need.
- **-efi variants** — the same images
  built for UEFI boot instead of the
  classic BIOS boot. Pick the one your
  machine uses.

For a router, do not navigate this by
guessing: the project's **Firmware
Selector** (firmware-selector.openwrt.org)
lets you type a model name and hands
you the right files for the current
release, and the device page in the
wiki is the authority for its own
model, including whether the model
wants anything special at flash time.

## Proving the download

Every target directory carries a
`sha256sums` file: the project's
published checksum for every file in
it. Checking yours against it is not
paranoia decoration; it is how you
know a truncated download or a wrong
file before it is inside a router.

The lab did it in transcript **t00**
(`openwrt-lab/transcripts/`): fetched
the `sha256sums` for 25.12.6 x86/64,
downloaded the 12 MB combined image,
and ran the check:

```
openwrt-25.12.6-x86-64-generic-squashfs-combined.img.gz: OK
```

`OK` is the whole ceremony. On your own
machine the same check is
`sha256sum -c sha256sums` (Linux),
`shasum -a 256 -c` (macOS) or
`Get-FileHash` (Windows) — and for the
truly careful, `sha256sums` itself is
signed (`sha256sums.asc`), which the
project's download page explains how
to verify.

One habit worth forming here: keep the
file name. A folder of images named
`openwrt.img`, `openwrt2.img` and
`final.img` is a future accident. The
project's names are verbose because
every part of them is a fact you will
want later — version, target, device,
layout.
