# Glossary

**Outcome: every term the guide used,
defined in the guide's own terms.**

**apk** — the package manager of
OpenWrt 25.12 and later, borrowed from
Alpine Linux. Manages `.apk` packages
against the feeds; its list of
explicitly requested packages is the
**world file**, `/etc/apk/world`.
Replaced **opkg**.

**Attended SysUpgrade** — the upgrade
service (and its clients, LuCI's page
and `owut`) that builds a fresh image
containing *your* package list on the
project's servers, so an upgrade keeps
your software as well as your settings.

**board.json / board.d** — the record
of the detected hardware, written at
first boot by the scripts in
`/etc/board.d/`, and the input from
which default configuration is
generated.

**BusyBox** — the single multi-tool
binary providing the shell and most
core utilities in the base system;
why a router's userland feels like a
small, tidy subset of Linux.

**config_generate** — the first-boot
program (`/bin/config_generate`) that
turns board.json into the initial
`/etc/config/network` and `system`
files.

**device profile** — the exact
hardware definition an image is built
for; on router targets, the model (and
sometimes hardware revision) identifier
used in download paths and ImageBuilder
`PROFILE=`.

**dnsmasq** — the combined DHCP and
DNS server of the LAN side in the
stock system.

**dropbear** — the small SSH server
(and client) shipped instead of
OpenSSH.

**DSA** — Distributed Switch
Architecture, the kernel framework
through which switch ports appear as
network devices; its adoption in
21.02 is the ecosystem's standing
example of a default that moved.

**factory image** — the image flavour
installed *from stock firmware* (or a
recovery mode). Counterpart:
**sysupgrade image**, for devices
already running OpenWrt.

**failsafe mode** — a minimal boot —
fixed 192.168.1.1, no password, overlay
unmounted or mounted read-only — for
recovering from bad configuration.
Reached by button timing on routers,
or a boot-menu entry on x86.

**feed** — a package repository. A
target's release directory carries
several (base, packages, luci,
routing, telephony), named in the
device's `/etc/apk/repositories.d/`.

**firewall4 (fw4)** — the service that
compiles `/etc/config/firewall` into
**nftables** rules; `fw4 check`
validates the file.

**ImageBuilder** — the per-release,
per-target tarball that assembles
custom images from published packages
without compiling; chapter 10's tool,
and attended sysupgrade's engine.

**kmod** — a kernel module package;
the unit in which drivers arrive.

**LuCI** — the web interface: Lua/ucode
applications over **uhttpd** and
**rpcd**, writing the same UCI files
the shell tools write. A view, not a
separate system.

**musl** — the small C library the
whole system is built against, in
place of the desktop's glibc; the root
of "binaries from my PC don't run".

**netifd** — the daemon that owns
network interfaces: link state, DHCP
clients, addresses, and applying the
`network` configuration live.

**odhcpd** — the IPv6 neighbour: DHCPv6
server and router advertisements for
the LAN.

**opkg** — the package manager of
OpenWrt up to 24.10. Still what nearly
all older writing describes; absent
from 25.12.

**overlay** — the writable filesystem
layer (in `/overlay`) laid over the
read-only base by overlayfs; where
configuration and installed packages
live, and the first thing erased by a
reset.

**PARTUUID** — the partition identifier
by which the kernel finds its root
filesystem in the combined images;
written into the bootloader's command
line in chapter 5.

**procd** — the init system (PID 1):
starts and supervises services, reads
the scripts in `/etc/init.d/`, and
replaces the SysV machinery of older
Linux.

**/rom** — the mount point where the
pristine, read-only base filesystem
can be inspected on a running system.

**squashfs** — the compressed read-only
filesystem holding the base system in
most images; why the base cannot be
edited in place and why resets are
clean.

**sysupgrade** — the upgrade tool and
the act: verifies an image for the
device, preserves configuration,
writes flash, reboots. Also the author
of config backups (`-b`).

**target / subtarget** — the first two
components of an image's identity:
the hardware family (`x86`, `mediatek`)
and the variant within it (`64`,
`filogic`).

**UCI** — the Unified Configuration
Interface: the file format of
`/etc/config`, the `uci` command that
edits it, and the discipline that one
text file per service is the whole
configuration story.

**uci-defaults** — scripts in
`/etc/uci-defaults/`, run once at first
boot (and after upgrades, where they
migrate configuration between
versions), then deleted.

**uhttpd** — the small web server that
serves LuCI.

**zone** — the firewall's unit of
trust: a named set of networks (lan,
wan) with policies for traffic to,
from and between zones.
