# Packages with apk

**Outcome: you can say what software a
stock image actually contains, where
that list lives, and how more is
added — and you will not go looking
for opkg in a 25.12 system.**

For twenty years, "installing software
on OpenWrt" meant one tool: **opkg**.
Anyone who learned OpenWrt before 2025
has its commands in their fingers, and
most of the internet's advice still
speaks it. So the first fact of this
chapter is a changing of the guard,
proven from the image itself in
transcript **t04**:

```
$ ls -la usr/bin/apk usr/bin/uci usr/bin/opkg
ls: cannot access 'usr/bin/uci': No such file or directory
ls: cannot access 'usr/bin/opkg': No such file or directory
-rwxr-xr-x 1 root root 231731 Oct 17 21:45 usr/bin/apk

$ ls etc/opkg/
keys
```

(The `uci` tool is present, as chapter
6 assumed — it simply lives in
`/sbin`, as system tools do.)

**25.12's package manager is `apk`** —
apk-tools, the package manager of
Alpine Linux, in its version-3 form
with signed package indexes (`.adb`
files). Of opkg there remains an
`/etc/opkg/keys` directory holding
signing keys, and nothing else: no
program, no status file, no feed
configuration. The concepts did not
change — packages, dependencies,
feeds, a local database of what is
installed — but the tool, the file
formats and the database all did, and
on a 25.12 machine the opkg commands
in older tutorials will simply answer
"not found". The translation is mostly
mechanical:

| You want | Old (opkg) | 25.12 (apk) |
|---|---|---|
| Refresh the lists | `opkg update` | `apk update` |
| Install | `opkg install nano` | `apk add nano` |
| Remove | `opkg remove nano` | `apk del nano` |
| List installed | `opkg list-installed` | `apk info` |
| Search | `opkg list | grep x` | `apk search x` |
| Upgrade everything | (discouraged) | `apk upgrade` |

## What the stock image contains

The installed-package database is
`/lib/apk/db/installed`, a text file
of records (`P:` name, `V:` version,
and dependency, size and checksum
fields). Counting its records in t04:

```
$ grep -c "^P:" lib/apk/db/installed
182
```

**182 packages.** Reading the list is
the fastest honest tour of what
OpenWrt thinks a router is: BusyBox
(the single many-tools-in-one binary
that provides most small commands),
the kernel and its modules for the
target, `netifd`, `procd`, `dnsmasq`
2.93, `dropbear` (SSH), `firewall4`,
`odhcpd` and `odhcp6c` (IPv6), `uhttpd`
and the **LuCI** stack (this x86 image
ships the web interface by default),
`rpcd`, `ucode` and its module family
— ucode is the small scripting language
much of the modern system and LuCI is
written in — plus tools with specific
jobs: `owut`, the attended-upgrade
client of chapter 11, and the
`attendedsysupgrade-common` support
package.

A second file separates *asked for*
from *merely needed*. `/etc/apk/world`
lists the packages the image build
explicitly requested — the top-level
choices, from `base-files` to `owut` —
while the other hundred-odd entries in
the database are dependencies those
choices pulled in. apk tracks that
distinction on a running system too:
`apk del` removes a package and any
dependencies nothing else wants, and
**the world file is what an upgrade
must preserve**, which is why
chapter 10's custom image and
chapter 11's upgrades both think in
terms of it.

## Feeds

Available-but-not-installed packages
come from **feeds**: signed indexes on
the project's servers, listed under
`/etc/apk/repositories.d/`. The stock
`distfeeds.list` (t04) points at the
25.12.6 release tree on
downloads.openwrt.org —
`.../targets/x86/64/packages/` for
target-specific packages, with the
architecture-wide collections alongside
it — and a `customfeeds.list` waits,
empty but for instructions, for any
repository of your own. Two
consequences worth knowing before you
need them:

- **Release feeds are forever.**
  Because they hang off the release
  directory, a 25.12.6 system can still
  fetch its packages years later.
  Snapshot feeds are the opposite —
  rebuilt daily, old ones deleted —
  which is why snapshot users install
  everything promptly or build images.
- **Installing writes to the overlay.**
  Chapter 3's writable layer is where
  every added package lands. On a
  16 MB-flash router the base system's
  economy stops being a curiosity and
  becomes your budget; `apk info` and
  a filesystem check are how you watch
  it. (On the lab's x86 image the
  overlay-to-be is a roomy 104 MB
  partition, of which the read-only
  part uses under 6 MB.)

LuCI has a **Package Manager** page
(`luci-app-package-manager` is in the
stock list) that drives the same apk
database from the web, for readers who
would rather click — the state it
shows is the state described here.
