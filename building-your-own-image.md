# Building Your Own Image

**Outcome: you can use the ImageBuilder
to produce an image with your packages
already inside — and you know the two
honest caveats the lab found: the
default build is leaner than the
official image, and the proof of a
build is in its manifest, not its
exit code.**

Chapters 4–7 treated the official
image as a found object. This chapter
makes one. The tool is the
**ImageBuilder**: a tarball, per
release and target, containing the
release's package feeds and the
machinery to assemble images from
them — no compiler, no kernel build,
nothing compiled at all. It exists
because of a common wish: *the stock
image, plus my packages, minus nothing
I need*, baked in so a reset or a
fresh install returns *my* system, and
so the router never has to spend its
small overlay on what could have been
in the read-only base.

## The lab's build, and its first failure

Transcript t07 downloads the
ImageBuilder for 25.12.6 x86/64 the
same way chapter 4 downloaded the
image — it is listed in the same
`sha256sums`, and it is verified
against the same file before use
(42 MB compressed, about 200 MB
unpacked). The customisation chosen
was deliberately small: add **nano**
and **htop**, two utilities the stock
image provably lacks (chapter 7's
package list).

The first build failed before doing
anything, and its failure is kept
verbatim in transcript t08 because it
is the kind of failure worth meeting
here rather than at midnight:

```
Build dependency: Please build with umask 022 - other values produce broken packages
Prerequisite check failed. Use FORCE=1 to override.
make: *** [.../toplevel.mk:193: check] Error 1
```

The build refuses to start under a
file-creation mask it considers unsafe
for the packages it writes. The fix is
the one it asks for — `umask 022`, then
again:

```
$ make image PROFILE="generic" PACKAGES="nano htop"
```

(`PROFILE` names the device; `generic`
is the x86/64 catch-all. On router
targets the profile is the exact model
identifier from the device page, the
same string chapter 2 kept warning
about.) A few minutes of downloading
packages and assembling filesystems
later, `bin/targets/x86/64/` held a
full set of fresh images and — the
file this chapter actually cares about
— a **manifest**.

## Proof, and a surprise

The manifest is the build's bill of
materials, one `name - version` per
line. Transcript t09 checks it:

```
14:htop - 3.5.3-r1
103:libncurses6 - 6.4-r3
121:nano - 9.3-r1
134:terminfo - 6.4-r3
```

The two requested packages are in, and
so are their two dependencies, resolved
automatically — that part of the
promise kept itself. But the manifest
has **148** entries where the stock
image has **182** packages, and the
diff (also t09) shows why: a plain
`make image` builds the ImageBuilder's
*default* package set, and the official
release image is **not** that default.
Thirty-eight stock packages are
absent, and they are nearly all one
thing: **the entire LuCI web stack** —
`luci`, `luci-base`, the modules,
`uhttpd`, `rpcd` — plus the attended
upgrade machinery. The release builders
add them; the ImageBuilder, left to
itself, makes the lean engine-room
system. (This is a famous small shock
to new builders, and the reason "I
built my own image and the web
interface is gone" is a rite of
passage. Nothing is broken. The web
interface is a package set like any
other.)

The second build therefore asked for
what the official image actually is,
plus the lab's two utilities:

```
$ make image PROFILE="generic" PACKAGES="luci nano htop"
```

Transcript t10: **177** packages in
the new manifest, reconciling exactly
against stock — 182, minus nine the
custom build still leaves out (the
attended-upgrade trio, LuCI's SSL
pieces, the GRUB packages, two support
modules — each addable by name the
same way), plus the four additions.
And transcript t11 closes the loop the
only way that counts: the built image
was unpacked with chapter 5's method,
and inside its root filesystem sit
`/usr/bin/nano` (151,657 bytes) and
`/usr/bin/htop` (295,377 bytes), at
paths where the stock rootfs answers
*No such file or directory*.

## Using it well

- **The manifest is your receipt.**
  Diff it against the previous build's
  before flashing anything; an
  unexpected absence is how a package
  name typo announces itself.
- **Add collections deliberately.**
  `luci` is a metapackage; router
  builds typically name their device
  profile and add `luci` plus a short
  list — a VPN, a monitor, an editor —
  and stop. An image is not improved
  by being full; the overlay still has
  to live somewhere.
- **Files can be baked too**: a
  `files/` tree in the build directory
  is overlaid on the rootfs, which is
  how a pre-set configuration — even a
  whole `/etc/config` — ships inside an
  image. Powerful, and exactly as
  dangerous as it sounds: an image
  with your password hash baked in is
  a secret with a download count.
- **This is attended sysupgrade's
  engine** (chapter 11): the service
  runs this same builder with your
  package list. Having built by hand
  once, you know precisely what it is
  doing on your behalf — and what to
  check when its manifest comes back
  different from what you expected.
