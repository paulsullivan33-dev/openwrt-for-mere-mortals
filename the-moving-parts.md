# The Moving Parts

**Outcome: you can name the pieces an
OpenWrt system is made of and say which
piece holds what — so that later
confusion is a named problem, not fog.**

An OpenWrt installation is small enough
to hold in your head whole. These are
its parts, in the order a booting
machine meets them. Every part named
here is one the lab later reads out of
a real image (chapters 5 to 7).

## The firmware image

The unit of everything. A single file,
built for one **target** (a family of
hardware, like `x86/64`) and usually
one **device** within it, containing a
kernel and a root filesystem. You met
its anatomy coming in chapter 5; for
now, hold the shape: on x86 it is a
small disk with two partitions — a
boot partition with the **GRUB**
bootloader and the kernel, and a root
partition with everything else. On
most routers there is no disk image at
all, just kernel and root filesystem
written to raw flash partitions the
device's own bootloader understands.

## The split root: /rom and the overlay

The root filesystem inside the image
is **squashfs**: compressed, and
read-only by design. When the machine
runs, that read-only part is mounted
as `/rom`, and a small writable area
— the **overlay**, kept after the
root filesystem on flash, or in a
second partition — is laid over it,
so the running system sees one `/`
that it can write to. Every change you
make lives in the overlay; the base
never changes. Two consequences run
through the whole system:

- **Reset to defaults means erasing
  the overlay.** Nothing else needs
  restoring, because nothing else was
  ever written.
- **Space is counted twice over**: the
  compressed size is what the image
  boasts about, the overlay is where
  your packages and files actually go,
  and both are smaller than you hope
  (chapter 14).

## procd, the first process

A small **init** stub starts **procd**,
OpenWrt's process supervisor (the role
systemd plays on a desktop Linux, at a
fraction of the size). procd starts the
services from `/etc/init.d`, watches
them, restarts the ones configured to
be restarted, collects the system log,
and handles the power button and the
reset button. The list of services in
the image — and the order they start —
is readable in chapter 5's evidence.

## UCI, the configuration system

Nearly all configuration lives in a
handful of text files under
`/etc/config/`, written in one strict
format, all of them editable through
one tool, **uci**, and one web
interface. One file per subject:
`network`, `dhcp`, `firewall`,
`wireless`, `system`, and one per
service that wants one. Chapter 6
reads the shipped files whole —
including the discovery that `network`
and `system` are not shipped at all
but generated on first boot.

## netifd, dnsmasq, and the firewall

Three workers do the actual routing:

- **netifd** reads `network` and
  configures interfaces, bridges,
  addresses and routes, bringing links
  up and down as cables and radios
  come and go.
- **dnsmasq** is the house's DNS
  resolver and DHCP server in one
  small program, configured by the
  `dhcp` file.
- **firewall4** (the command is `fw4`)
  turns the `firewall` file into
  **nftables** rules — the Linux
  kernel's packet filter — with zones,
  so you describe *lan may reach wan*
  and it writes the hundreds of rules
  that mean it.

Around them: **dropbear**, a small SSH
server; **uhttpd**, a small web server
that serves **LuCI**, the web
interface; and **rpcd**, the bus LuCI
uses to talk to the system. LuCI is a
face on the same UCI files, not a
second configuration: anything done in
the web pages can be read back in the
text files afterwards, which is why
this guide teaches the files first.

## apk, the package manager

Software beyond the base image is
installed with **apk** — the Alpine
Linux package manager, which OpenWrt
25.12 adopted in place of the old
**opkg** it used for two decades. The
switch is visible in the image itself
(chapter 7): there is an `apk` program
and an APK database, and of opkg only
an empty keys directory remains.
Packages are small, dependencies are
tracked, and installing one writes
into the overlay — which is why "it
worked until I rebooted after filling
the overlay" is a rite of passage.

## Where the pieces keep their state

- `/etc/config/` — configuration (in
  the overlay once you change it).
- `/tmp` — memory-backed scratch: DHCP
  leases, logs by default, the resolver
  files. Gone at reboot, by design.
- `/overlay` — the writable layer
  itself, visible on the running
  system if you go looking.
- The **boot partition** (x86) or the
  flash layout (routers) — kernel and
  bootloader, touched only by upgrades.

With those names in hand, the next
chapter goes and gets the real thing.
