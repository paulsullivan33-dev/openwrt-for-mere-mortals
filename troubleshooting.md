# Troubleshooting

**Outcome: the dozen failures people
actually meet, in the order they meet
them, each with the check that
separates it from its neighbours.**

**The download "doesn't work" on the
device.** Checksum first, always:
`sha256sum` the file against the
published sums (chapter 4). A mismatch
is a corrupt or wrong download, not a
mysterious device. Second: is it the
image for *this* target, profile and
hardware revision, and the *sysupgrade*
flavour for a device already running
OpenWrt, *factory* for one that is
not? Most "bad image" stories are one
of those three.

**The router never comes back after
flashing.** Do not keep power-cycling
it. Wait longer than feels reasonable
(first boots generate keys and
configuration; on slow flash, minutes).
Then failsafe (chapter 11): the boot
button ritual from the device page, or
the GRUB failsafe entry on x86. A
router that answers at 192.168.1.1 in
failsafe is not bricked; it is waiting
to be told what you did.

**No web interface after installing.**
Three usual causes, three checks. The
image is an ImageBuilder default
without LuCI (chapter 10's surprise):
`apk info | grep luci` on the shell
says so in one line, and the cure is
adding the packages or using the
official image. The web server is
down: `service uhttpd status` / check
`/etc/config/uhttpd` (the shipped
default listens on the LAN addresses,
transcript t03). Or you are asking the
wrong address — after recipe 3, the
router lives where you moved it.

**"I edited the config and nothing
changed."** Two mechanisms, both
chapter 6. The change was staged, not
committed (`uci changes` shows what
is still only staged). Or it was
committed and the *service* was never
told — commit normally handles it via
ucitrack, but a hand-rolled edit made
while the service keeps its own state
may need the service restarted. And
the mirror-image failure — "my edit
vanished" — is an editor writing the
read-only `/rom` copy's path, or a
change made inside a failsafe session,
where the overlay was never mounted.

**`opkg: not found`.** You are on
25.12 or later, where the package
manager is **apk** (chapter 7), or
reading old advice. Everything written
for opkg before 2025 needs translating:
`apk add`, `apk del`, `apk info`, and
the world file at `/etc/apk/world`.

**"No space left" while installing a
small package.** The base system is
read-only; deleting stock files frees
nothing. The budget is the overlay:
`df -h /overlay` tells the truth, the
usual spenders are old packages and
logs, and the permanent cure for a
recurring need is chapter 10 — bake it
into the image, where it costs ROM
instead of overlay.

**There is no `/etc/config/network`.**
On a dissected image: correct, it is
generated at first boot (chapter 6).
On a *running* system: something
deleted it; rebooting regenerates the
device defaults (your changes are in
the overlay copy that was deleted —
this is why the backup in recipe 8
exists), and `cat /etc/board.json`
plus `/bin/config_generate` are the
machinery to inspect.

**There is no `/etc/config/wireless`.**
Expected without radios (the lab's
running system said so too, transcript
t13), and on a router: generated when
the wifi machinery scans real hardware
(`wifi config`). If a router with
radios shows none, the radios were not
detected — wrong image for the model
is the first suspicion, not the last.

**Time is wrong and nothing will
authenticate.** The generated `system`
config defaults to GMT0/UTC with NTP
enabled (transcript t12); set the
timezone properly (LuCI's system page,
or `zonename` in UCI), and check the
WAN can actually reach the NTP pool —
a router with no route has no clock,
and a wrong clock breaks certificates,
which breaks everything that reports
it as a different, stranger error.

**The ImageBuilder refuses to start.**
`Please build with umask 022` is a
prerequisite check with the fix inside
the message (transcript t08): set the
umask and rerun; do not reach for
`FORCE=1` until you know which check
you are overriding.

**Everything works except one thing
after an upgrade.** Read the release
notes *now* — the late edition. Then
`uci show` the affected file and look
for the option whose name or meaning
moved (chapter 11's migrations), and
check `logread` for the service
explaining itself at boot. The config
backup from before the upgrade diffs
beautifully against the present.

**When to stop and ask.** Repeated
failsafe entries that do not hold,
flash errors mid-write, a device that
loses its network ports' link lights
entirely — hardware and bootloader
territory, where the device page and
the forum's model-specific threads
outrank any general guide, this one
included.
