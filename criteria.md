# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The lab's example network is the stock 192.168.1.0/24 and
   its example machines are a printer and a game server.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 14 names what the system
   cannot do: per-device images with no universal installer,
   the small overlay budget, a musl/BusyBox userland that runs
   no desktop binaries, wireless quality bounded by open
   drivers, defaults that move between releases, and a fresh
   system whose root account starts with no password.
8. **The safety rules are in the text, early.** A router is
   other people's connectivity: chapter 2 puts its checks
   before any purchase or flash (exact model and revision, a
   known recovery path, the device page read first), chapter 9
   states that the wireless country code is law as well as
   physics, and the no-password default is flagged where a
   reader first meets the system, not saved for the fine print.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

The build machine is a 2-CPU, ~8 GB Ubuntu 24.04 sandbox,
shared with other work while the lab ran. The subject is
**OpenWrt 25.12.6** (build `r33311-981c978eff`), target
x86/64, chosen because it was the newest stable point release
listed at downloads.openwrt.org on the day the lab ran. The
lab had **no router hardware, flashed nothing, and touched no
real network**: the emulated boot used QEMU's internal
user-mode networking only. All transcripts live in
`openwrt-lab/transcripts/` (t00–t13).

Run for real, with transcripts as evidence:

- Download of the release `sha256sums` (190,950 bytes) and of
  `openwrt-25.12.6-x86-64-generic-squashfs-combined.img.gz`
  (12,303,031 bytes); the published SHA-256
  `56cf27d6f76aa582eea4c5ea18bc5f76065df48e87bf3d84e1dec82ab7f7ead4`
  verified with `sha256sum -c` → OK (t00).
- Image anatomy: DOS/MBR, disk label-id `0x011cbbbd`,
  partition 1 bootable 16 MB at sector 512, partition 2
  104 MB at sector 33,792 (t01). Partition 2 unpacked
  (`dd` + `unsquashfs`): squashfs 4.0, xz, 1,291 inodes;
  `/etc/os-release` read (VERSION 25.12.6, kernel modules
  6.12.112) (t02).
- The shipped configuration read verbatim: `/etc/config/`
  holds exactly dhcp, dropbear, firewall, luci, rpcd, uhttpd;
  **`network`, `system` and `wireless` are absent**, and the
  generator (`/bin/config_generate`, `/etc/board.d/`) that
  creates the first two was read and quoted — LAN default
  192.168.1.1, hostname OpenWrt, GMT0; DHCP pool .100–.249/12h;
  the complete 189-line firewall file (t03).
- The package system: **25.12 uses apk, not opkg** — no opkg
  binary exists; `/lib/apk/db/installed` lists 182 packages;
  versions quoted for dnsmasq, dropbear, firewall4, luci-base,
  netifd, procd, owut; the stock image contains **no
  hostapd/wpad** (t04).
- Boot partition read without mounting (debugfs): GRUB
  config, serial 115200, `root=PARTUUID=011cbbbd-02`, and a
  failsafe menu entry (t05). Init: procd, the inittab
  (serial login on ttyS0), init.d and uci-defaults listings,
  `/sbin/sysupgrade` present (t06).
- ImageBuilder 25.12.6 x86/64 downloaded and verified against
  the same published sums (t07). First build attempt failed
  on the umask 022 prerequisite check — kept verbatim (t08).
- Custom build 1 (`PACKAGES="nano htop"`): exit 0; manifest
  of 148 packages includes nano 9.3-r1, htop 3.5.3-r1 and
  their dependencies; diffed against the stock 182 — the
  ImageBuilder default omits the whole LuCI stack the
  release build adds (t09).
- Custom build 2 (`PACKAGES="luci nano htop"`): manifest of
  177, reconciling exactly (182 stock − 9 still omitted +
  4 added) (t10). The built image unpacked the same way as
  the stock one: `/usr/bin/nano` and `/usr/bin/htop` exist
  in its rootfs and not in the stock rootfs (t11).
- The stock image booted under QEMU 8.2.2 (TCG, no KVM) to a
  root shell on the serial console, twice (t12, t13). Real
  runtime output captured: `ls /etc/config/` showing the
  generated `network` and `system` (and still no
  `wireless`); the generated `/etc/config/network` verbatim
  (br-lan, eth0, 192.168.1.1/24, a minted ULA prefix); `uci
  show network` and `uci show system`; `br-lan` up with
  inet 192.168.1.1/24; `ubus call network.interface.lan
  status` reporting `"up": true`; dnsmasq running in
  `logread`. On this virtual hardware no `wan` stanza was
  generated, and the chapters say so rather than tidying it
  away.

Documented only — described from the project's documentation
and the verified files, not run, and the text does not claim
otherwise:

- Flashing any real device, by any method; first boot on real
  flash; recovery and failsafe on physical hardware (the
  x86 GRUB failsafe entry is verified present in the image;
  button-timing failsafe on routers is documented).
- LuCI as a clicked web interface. Its packages, server and
  configuration are verified present and read; no browser
  session was driven.
- Wireless on real radios: configuration generation on
  hardware that has radios, hostapd behaviour, throughput,
  roaming, mesh. Chapter 9 is the guide's most
  documented-only chapter and says so.
- sysupgrade and Attended SysUpgrade as performed
  procedures: the tools are verified present in the image
  and their documented behaviour described; no upgrade was
  executed.
- The recipes of chapter 12: assembled from the verified
  files and UCI grammar, labelled in the chapter as not
  executed against a router.

One correction culture note: two early lab artefacts (a
transcript truncated by a pipe, a wrong working directory)
were regenerated rather than patched, and the failures that
taught something — the umask refusal above all — were kept
verbatim in the transcripts on purpose.

Fixtures are this guide's own and deliberately generic: a
printer, a game server on the stock port 25565, and the
stock LAN addressing. No real household, network or
organisation appears anywhere in the guide.
