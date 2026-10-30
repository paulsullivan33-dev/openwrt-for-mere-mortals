# Limits

**Outcome: you can recite what OpenWrt
cannot do, will not do, and what this
guide could not prove — before any of
them surprises you.**

## Limits of this guide's evidence

The lab verified the *software*: a
real download, checksum-proven,
dissected, rebuilt to order. It had
**no router, and flashed nothing**.
Everything about the moment of
flashing a real device — the upload,
the wait, the first boot on real
flash, radio behaviour, LED and button
behaviour, throughput — is documented
from the project's own material and
labelled as such in the chapters and
in `criteria.md`. LuCI is proven to
exist and to be made of UCI like
everything else; it was never clicked
through. Treat any sentence in this
guide about a physical box as
well-founded description, not as a
measurement, and the device page for
your model outranks all of it.

## Limits of the system

- **One image per device.** An image
  is built for a target and a device
  profile. There is no universal
  installer, no live USB that adapts,
  and the wrong image ranges from
  refused (good) to accepted and wrong
  (a brick) on hardware whose stock
  firmware did not check. Identification
  is the user's job, every time.
- **Small rooms.** The base system is
  frugal, but the overlay that receives
  your packages is what remains of
  the flash after it. On 16 MB devices
  a handful of packages is a budget;
  things like full Python, Docker or a
  large VPN suite can simply not fit,
  and no setting changes arithmetic.
- **A small userland, on purpose.**
  The C library is musl, the shell and
  core tools are BusyBox. Binaries
  built for desktop Linux do not run;
  scripts assuming the GNU flavour of
  a tool occasionally trip on the
  BusyBox one; some software never
  arrives because its authors test on
  neither. This is the price of
  fitting in a router, charged daily.
- **Wireless is the vendor's chip,**
  not the project's promise (chapters
  2 and 9): open drivers vary by radio
  family, some features of stock
  firmware (proprietary acceleration,
  some mesh and band tricks) have no
  open equivalent, and hardware NAT
  offload is likewise chipset-specific.
  On paper-identical routers, OpenWrt
  throughput can trail stock for these
  reasons. For many households it does
  not matter; for gigabit fibre and a
  weak CPU, it can.
- **Defaults move between releases.**
  The DSA switch rework remains the
  standing example; configuration is
  migrated where the project can and
  documented where it cannot. Skipping
  release notes and keeping settings
  across a major upgrade is the most
  common self-inflicted wound in the
  ecosystem.
- **Security is a practice, not a
  property.** A fresh OpenWrt has root
  with **no password** — anyone on the
  LAN side can log in until you set
  one, which the web interface nags
  you to do at first login and chapter
  12's recipes do first for a reason.
  Nothing updates itself: new releases
  are the administrator's job, forever.
  And the entire model assumes the LAN
  is the trusted side — a setting the
  firewall file makes easy to change
  and easier to forget you changed.
- **Snapshots are not a release.**
  They are rebuilt daily, their package
  feeds evaporate, and support for them
  is "build it again tomorrow". They
  exist for developers and the
  desperate, in that order.

## Limits of the shape

OpenWrt's answers are a text file, a
small daemon and a zone diagram. That
shape is its strength — chapter 8's
firewall can be read in a sitting —
and its boundary: it is not a
next-generation firewall with
application inspection, not a network
management platform for a fleet of
sites, and not the tool for making a
ten-year-old router feel new. Inside
those lines it is exceptionally good
at its job. The lines are just real.
