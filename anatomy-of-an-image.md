# Anatomy of a Real Image

**Outcome: you can take a firmware
image apart on an ordinary computer —
no router, no flashing — and point at
the bootloader, the kernel, the root
filesystem and the release's identity
card.**

Everything in this chapter was done to
the lab's verified download on a
regular Linux machine. The transcripts
are **t01** and **t02** (the cutting
up) and **t05** (the boot partition).

## A disk, not a blob

The uncompressed combined image is
120.5 MB, and `fdisk` reads it as what
it is: a small DOS-partitioned disk.

```
Device        Boot Start   End Sectors  Size Type
...img1       *      512 33279   32768   16M Linux
...img2           33792 246783  212992  104M Linux
```

- **Partition 1 (16 MB)** is the boot
  partition: an ext2 filesystem whose
  volume label is literally `kernel`.
  You do not need to mount it to read
  it — `debugfs` lists and extracts
  files from ext filesystems directly,
  and transcript t05 does exactly
  that. Inside `/boot` sits one file,
  `vmlinuz`, and `/boot/grub` holds
  GRUB's `boot.img`, `core.img` and
  `grub.cfg`.
- **Partition 2 (104 MB)** begins with
  the squashfs root filesystem.

The shipped `grub.cfg` is eleven lines
and worth reading whole (t05 prints
it). It turns on the serial console,
waits five seconds, and boots the
kernel with:

```
linux /boot/vmlinuz root=PARTUUID=011cbbbd-02 rootwait console=tty1 console=ttyS0,115200n8 noinitrd
```

Three facts hide in that line. The
root device is named by **PARTUUID** —
partition 2 of a disk whose label-id
is `011cbbbd`, the very value `sfdisk`
reported for this image in t01, so the
bootloader and the partition table
agree about which disk this is. The
kernel is told to speak on **both**
the screen (`tty1`) and the serial
port (`ttyS0`) — remember that in
chapter 6, where this exact image is
made to run and talk to a terminal. And there
is a second menu entry, **OpenWrt
(failsafe)**, differing only by
`failsafe=true`: the recovery mode is
sitting in the boot menu of every
stock image, before anything has gone
wrong.

## The root filesystem

Cut out at its partition offset
(33,792 sectors in), partition 2's
payload identifies itself:

```
Squashfs filesystem, little endian, version 4.0,
xz compressed, 6096242 bytes, 1291 inodes,
blocksize: 262144 bytes
```

Notice the sizes: the filesystem is
**5.8 MB** inside a **104 MB**
partition. That is not waste. The
space after the squashfs is where the
writable overlay will be created on
first boot (chapter 3), and on a PC
image it is sized generously because
disks are big. On a router the same
layout is squeezed into whatever flash
remains — which is why chapter 2 made
such a fuss about flash size.

Unpacking with `unsquashfs` gives the
whole system as ordinary directories —
`bin`, `etc`, `lib`, `sbin`, `usr`,
`www`, and the empty mount points
(`proc`, `sys`, `overlay`, `rom`,
`mnt`, `tmp`). This is the single most
useful trick in the guide: **any
question about what an OpenWrt release
does by default can be answered by
unpacking its image and reading it**,
with no hardware and no risk. The next
three chapters are exactly that,
applied to configuration, packages and
services.

## The identity card

Two files in `/etc` state what the
image believes it is. `/etc/openwrt_release`
is the short one:

```
DISTRIB_ID='OpenWrt'
DISTRIB_RELEASE='25.12.6'
DISTRIB_REVISION='r33311-981c978eff'
DISTRIB_TARGET='x86/64'
DISTRIB_ARCH='x86_64'
```

and `/etc/os-release` is the long
standard form, adding the build number
(`BUILD_ID="r33311-981c978eff"`), the
board (`OPENWRT_BOARD="x86/64"`) and
the support URLs. `/etc/banner` is the
greeting a login sees, ASCII art and
all, ending with the release line and
its codename:

```
OpenWrt 25.12.6, r33311-981c978eff Dave's Guitar
```

The kernel, for the record, is
**6.12.112** — the image's
`/lib/modules/` directory is named for
it. Kernel modules in the image live
there and in `/etc/modules.d`, loaded
on demand for the hardware the machine
actually has.

A last piece of anatomy, in
`/etc/inittab` (transcript t06): the
consoles the system will offer a login
on are `ttyS0` (serial), `hvc0` (a
virtual-machine console) and `tty1`
(the screen). The image arrives ready
to be talked to by cable, by hypervisor
or by keyboard — which is what makes
the experiment in chapter 11 possible.
