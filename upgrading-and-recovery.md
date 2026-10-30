# Upgrading and Recovery

**Outcome: you can describe how an
OpenWrt system is upgraded without
losing its mind or its settings, what
the attended upgrade does differently,
and the three ways back when something
goes wrong.**

The lab did not upgrade anything —
there was nothing running to upgrade —
so the procedures here are documented,
but every tool they name is verified
present in the image: `/sbin/sysupgrade`
ships in the base system, the stock
`/etc/uci-defaults/` contains config
migration scripts, and the stock
package list includes the attended
upgrade machinery (`owut`,
`attendedsysupgrade-common`,
`luci-app-attendedsysupgrade`).

## sysupgrade, the normal road

Upgrading OpenWrt is not reinstalling.
The tool is **sysupgrade**: give it the
new image *for the same device* (it
checks; a mismatched image is refused
unless forced, and forcing is how
bricks are made) and it will:

1. Stash the configuration — by
   default the UCI files and everything
   listed for preservation in
   `/etc/sysupgrade.conf` — into a
   corner of memory.
2. Write the new image to flash.
3. Reboot, lay the saved configuration
   over the new system's defaults.

Installed packages are *not* in the
image and not in the stash: after a
plain sysupgrade the router comes back
with the release's stock package set
and your configuration pointing at
software that is no longer there.
`sysupgrade -l` lists what a backup
would keep, `sysupgrade -b file.tar.gz`
saves that backup by hand (do this
before any upgrade; a config backup is
small and is the real insurance), and
an option (`-k`) records the installed
package list into the backup for tools
that can act on it.

Release-to-release jumps are the
supported path — 23.05 to 24.10 to
25.12, one sysupgrade each, or even
skipping — **after reading the release
notes**. The notes matter because
defaults occasionally move house: the
famous case was the switch subsystem's
move to DSA in 21.02, which changed
how switch ports are named in the
network configuration, and every
release since has had smaller ones.
The migration is not magic; it is the
`/etc/uci-defaults/` scripts (chapter
6) in the new image rewriting old
configuration on first boot — the
stock 25.12.6 image ships migration
scripts for network bridges, DHCP and
timezone formats, readable in
transcript t06's directory listing,
which is the upgrade mechanism leaving
its working where you can read it.

## Attended sysupgrade: an image built for your router

The modern answer to "packages are
not preserved" is **Attended
SysUpgrade**. The client — in LuCI
under System, or the `owut` command —
sends the upgrade service
(sysupgrade.openwrt.org) three things:
the device, the target release, and
*your* package list and configuration
fingerprint. The service builds, on
the spot, an image that is the new
release **with your packages already
inside** — the ImageBuilder of
chapter 10, run by the project as a
service — and sysupgrade flashes it.
Configuration survives as usual;
packages survive because they were
never separate. It is the upgrade path
most home users should take, and the
reason this guide taught image
building by hand: attended upgrade is
that same machine with the typing done
for you.

## Recovery, in order of drama

**Failsafe mode.** A stock OpenWrt
that boots at all can be caught in
failsafe: on most routers, by pressing
the reset button at the moment the
boot messages or LED pattern invite it
(the device page gives the timing); on
the lab's x86 image, by choosing the
**OpenWrt (failsafe)** entry that
chapter 5 found sitting in GRUB's menu
of every stock image. Failsafe boots
with a minimal network — the router at
192.168.1.1, no password — and the
read-only base mounted, so you can
inspect the overlay, fix or erase the
configuration (`mount_root`, then
edit, or `firstboot` to wipe the
overlay and start clean), and reboot.
Erased-overlay recovery works precisely
because of chapter 3's design: nothing
precious lives outside the overlay.

**A saved configuration.** If the
system boots but is misconfigured, a
`sysupgrade -b` tarball from happier
days restores the configuration in one
step (`sysupgrade -r`), without
touching the firmware.

**Reflashing from outside.**
Manufacturers' recovery modes (many
routers will accept a correctly named
image over TFTP at boot), bootloaders
with their own web pages, and — the
last resort that almost always works —
a serial console: the image's
`inittab` (transcript t06) offers a
login on `ttyS0` by design, which is
why chapter 2 treated a serial header
as the difference between a project
and a paperweight. On x86 the whole
question is moot in the nicest way:
the "flash" is a disk, and any other
computer can rewrite it.

The recovery ladder is the practical
reason to prefer boring hardware
(chapter 2): every rung above exists
on a well-supported device, and the
rungs disappear one by one as the
device gets more exotic.
