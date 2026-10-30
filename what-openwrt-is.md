# What OpenWrt Is

**Outcome: you can say what OpenWrt is,
what it replaces, and why people accept
the trouble of installing it.**

Every router in every home is already a
small computer: a processor, memory, a
radio or two, and an operating system.
The operating system it arrives with —
the **stock firmware** — was chosen by
the manufacturer to be adequate, cheap
to support, and finished. It is rarely
updated for long, it cannot be inspected,
and when the manufacturer loses interest,
the box keeps routing with whatever
faults it had on the day it was
abandoned.

**OpenWrt is a replacement operating
system for that computer.** It is a
Linux distribution built for routers
and other small network devices, free
and open source, maintained since 2004.
You download a firmware image made for
your exact model, write it to the
device in place of the stock firmware
— **flashing** — and the box wakes up
running a real, inspectable Linux: a
shell you can log into, a package
manager, a firewall you can read, and
a configuration system this guide will
take apart file by file.

What you get in exchange for the
trouble:

- **Updates that outlive the vendor.**
  A maintained OpenWrt release supports
  hundreds of devices, many dropped by
  their manufacturers years ago.
- **One system everywhere.** The same
  commands, files and web interface on
  a cheap travel router and on a PC —
  the lab in this guide uses the x86/64
  image, and almost everything in it
  applies unchanged to a £30 router.
- **Nothing hidden.** The default
  firewall is a text file. The DHCP
  settings are a text file. This guide
  quotes both, complete, from a real
  download (chapter 6).
- **Room to grow.** Roughly two hundred
  packages are in the base image and
  thousands more install on demand —
  ad-blocking DNS, VPNs, dynamic DNS,
  traffic shaping, a small server or
  two.

What it costs: the first flash is the
riskiest ten minutes in a router's
life, done wrong it can leave the box
unbootable, and the warranty language
of most manufacturers washes its hands
of you. Chapter 2 is blunt about which
hardware makes this easy and which
makes it a project.

## What it is not

OpenWrt is not a program you install
inside the stock firmware, not a
jailbreak that keeps the vendor's
system underneath, and not a guarantee
of speed: it cannot give a weak radio
a stronger signal or a slow processor
more cycles. It also is not one file:
there is a different image for nearly
every model of router, and using the
wrong one is the classic way to have a
bad evening. Chapter 4 is about getting
exactly the right file and proving it
is the file the project published.

## The version this guide uses

OpenWrt releases are numbered by year
and month. The current stable series
when this guide was written is
**25.12**, and the lab downloaded the
newest point release, **25.12.6**
(kernels and versions are in chapter
5), from the project's own download
site and verified it against the
project's published checksums before
touching it. Release names are a
project tradition: 25.12's banner
calls itself "Dave's Guitar".

The rest of this guide works in a
particular order. Chapter 3 names the
moving parts. Chapters 4 and 5 obtain
a real image and dissect it without
running it. Chapter 6 reads its
configuration system — including the
surprise that two of the most famous
configuration files are not in the
image at all. Later chapters cover
packages, the network and firewall
shape, wireless, building an image of
your own, and how upgrades and
recovery work. Nothing in this guide
flashes a real router: the lab had no
router to flash, and chapter 14 says
plainly which parts that leaves
described rather than proven.
