# The Neighbours

**Outcome: you can place OpenWrt among
the other things a router can run, and
say in one sentence what each does
differently.**

**Stock firmware** is the neighbour
OpenWrt replaces: vendor-written,
model-specific, usually a Linux kernel
of some age under a private interface.
Its virtues are real — it knows the
hardware intimately, hardware
acceleration for NAT and Wi-Fi is
turned on and tuned, and it arrives
already installed. Its costs are the
ones chapter 1 listed: a closed
configuration, a short update life,
and no appeal when either ends. Many
households should honestly keep it;
the case for changing is inspectability
and longevity, not fashion.

**DD-WRT and FreshTomato** are the
elder alternative firmwares, from the
same 2000s moment that produced
OpenWrt, built around a web interface
first and a particular set of built-in
features. Where OpenWrt is a small
Linux distribution that happens to
route — packages, a shell, a system —
they are appliances: what the interface
offers is what there is. Both still
serve devices OpenWrt has dropped, and
FreshTomato's following among old
Broadcom hardware exists precisely
because it ships the proprietary
drivers OpenWrt cannot.

**OPNsense and pfSense** are the
firewall distributions of the x86
world: FreeBSD-based, stateful
firewalls with deep traffic tooling,
made to be administered as firewalls.
On a PC they out-firewall OpenWrt
comfortably and out-consume it too —
in disk, memory and power. The usual
division of labour: OpenWrt where the
box must be small, silent and wireless;
these where the box is a dedicated
firewall and wireless lives elsewhere.

**Derivatives and roles.** OpenWrt is
also a platform other projects build
on: **Gargoyle** re-skins it for
quota-and-monitoring households;
community mesh networks (**LibreMesh**,
Freifunk) run it by the thousand as
self-organising nodes; and the project
publishes a plain **rootfs tarball**
(it sat in the same download directory
as the lab's image) that runs OpenWrt
*inside* other systems — in a container
or a light virtual machine — for labs,
builds and odd jobs like this guide's.
A stock OpenWrt router also works
happily demoted: as a **dumb access
point** behind someone else's router
(DHCP off, firewall bypassed, radios
serving the LAN), which is the
honourable retirement of many a first
router when a bigger one arrives.

**The full build system** is the
neighbour inside the house: the
**SDK** and the source tree from which
the project itself, and chapter 10's
ImageBuilder, are cut. Building from
source gets you kernels, patches and
your own packages; it also gets you
compile times measured in hours and a
great deal of machinery this guide
deliberately stayed downstream of.
ImageBuilder is the right altitude for
almost everyone; the buildroot is
there when it stops being.

And one neighbour that is not
firmware: **the device page**. For any
specific router, the project's wiki
page for that model — install method,
recovery method, known faults — is a
more important document than this
guide, and the guide will keep sending
you to it. OpenWrt is a system; a
device is a case.
