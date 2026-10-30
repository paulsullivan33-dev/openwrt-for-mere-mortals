# UCI — the Configuration System

**Outcome: you can read any file in
`/etc/config/` on sight, say which of
them a release actually ships, and use
the `uci` tool's verbs — including the
one that makes a change real.**

UCI (Unified Configuration Interface)
is the idea that makes OpenWrt
learnable: instead of every program
inventing a config format, nearly all
of them read files from one directory,
in one grammar, through one library
and one command-line tool. Learn the
grammar once and the firewall, the
DHCP server and the web interface are
all the same language.

## The grammar, from a real file

Transcript **t03** prints the shipped
`/etc/config/dhcp` complete. Its shape:

```
config dnsmasq
	option domainneeded	1
	option boguspriv	1
	...
	option local	'/lan/'
	option domain	'lan'

config dhcp lan
	option interface	lan
	option start 	100
	option limit	150
	option leasetime	12h
```

Three constructs are the whole
language:

- **`config <type> [name]`** starts a
  **section** — one thing being
  configured. A section with no name
  (the `dnsmasq` one) is *anonymous*,
  referred to later by position, like
  `dhcp.@dnsmasq[0]`. A named section
  (`dhcp lan`, `dropbear main`) is
  referred to by name, like
  `dhcp.lan`.
- **`option <key> <value>`** sets one
  value in the section.
- **`list <key> <value>`** (the
  firewall file uses it for zone
  networks) adds one item to a list;
  repeat it for more items.

That is all: no nesting, no brackets,
nothing evaluated. The files are also
shell-shaped on purpose — UCI's parser
and the shell scripts that consume
these files both read them comfortably.

Reading the `dhcp` file now tells you
real things about a fresh OpenWrt:
DNS and DHCP are dnsmasq's job; the
local domain is `lan`; the DHCP pool
on the LAN is 150 addresses starting
at offset 100 into the network —
which, on the default network below,
means **192.168.1.100 to
192.168.1.249**, with 12-hour leases;
and the WAN side is told `ignore 1`:
no DHCP service offered to the
outside world.

## The files that are not there

Here is the surprise t03 records. A
stock image's `/etc/config/` contains
exactly six files — `dhcp`, `dropbear`,
`firewall`, `luci`, `rpcd`, `uhttpd` —
and:

```
$ cat etc/config/network
cat: etc/config/network: No such file or directory
$ cat etc/config/system
cat: etc/config/system: No such file or directory
$ cat etc/config/wireless
cat: etc/config/wireless: No such file or directory
```

**`network` and `system` are generated
on first boot**, because their contents
depend on the hardware under them.
The pipeline is in the image and t03
quotes its working parts: scripts in
`/etc/board.d/` inspect the board and
write `/etc/board.json`; at first boot
`/bin/config_generate` turns that into
UCI. The generic x86 board script
(`99-default_network`) assigns LAN to
`eth0` and WAN to `eth1` if it exists;
the generator's own defaults, read
straight out of its code in t03, supply
the rest: LAN protocol **static** with
address **192.168.1.1**, WAN protocol
**dhcp**, hostname **OpenWrt**,
timezone GMT0. Per-model board scripts
(the same directory carries pages of
cases for real appliances) override
the port assignments for their own
hardware.

And `wireless` does not exist because
there is nothing to describe: it is
created by the `wifi config` machinery
when a radio is found, and the x86/64
generic image has no radio — and, as
chapter 7 shows, not even the
access-point daemon installed. Chapter
9 returns to this.

A third kind of first-boot writing
lives in `/etc/uci-defaults/`: small
scripts that run exactly once (the
image ships ones that set up the web
server's bus access, migrate old
formats, generate a DHCP unique ID and
more), then delete themselves. When a
package needs to change defaults at
install time rather than ship a file,
this is where it does it.

## The tool

On a running system (the end of this
chapter shows real output from one)
the verbs are:

```
uci show network              # print a whole file, flattened
uci get network.lan.ipaddr    # one value
uci set network.lan.ipaddr='192.168.1.2'
uci add_list firewall.@zone[1].network='wan6'
uci delete dhcp.lan
uci changes                   # what is staged, not yet real
uci commit network            # make it real, tell the service
```

Two properties matter. **Staging**:
`set` edits a shadow copy (kept in
`/tmp/.uci`); nothing reads it until
`commit`, and `uci changes` shows the
pending diff — so a session of edits
lands atomically or not at all, and
`revert` throws them away. **Notification**:
committing is what tells the affected
service to re-read and re-apply (the
image ships a `ucitrack` service whose
whole job is knowing which service
cares about which file). This is also
exactly what LuCI does when you press
Save & Apply: the web pages are a
form over these same verbs, which is
why nothing done in LuCI is a mystery
afterwards — `uci show` will recite it
back to you.

One caution the grammar hides: editing
the files with a text editor works
too — they are just files — but a
malformed edit is only discovered when
something tries to parse it, often at
boot. The `uci` tool validates as it
goes, which is the practical reason to
prefer it for anything you cannot
afford to have silently ignored.

## A running system, once

Everything above about `network` and
`system` came from the generator's
code, because the files do not exist
until first boot. So the lab booted
the image — chapter 5's exact
download, run in an emulator (QEMU,
no real network attached) — and read
them there. Transcripts t12 and t13
are the full sessions. The generated
`/etc/config/network`, verbatim from
the running system:

```
config interface 'loopback'
	option device 'lo'
	option proto 'static'
	list ipaddr '127.0.0.1/8'

config globals 'globals'
	option dhcp_default_duid '00044c6b344e...'
	option ula_prefix 'fdd7:ec60:b15b::/48'

config device
	option name 'br-lan'
	option type 'bridge'
	list ports 'eth0'

config interface 'lan'
	option device 'br-lan'
	option proto 'static'
	list ipaddr '192.168.1.1/24'
	option ip6assign '60'
```

Exactly the generator's promises —
the bridge, the LAN at 192.168.1.1/24,
a random IPv6 ULA prefix minted at
first boot — and on this virtual
hardware, nothing else: no `wan`
stanza was generated (a second network
card recognised at generation time
would have produced one, per the code
chapter 8 reads). And the config was
not merely written but *in force*:

```
4: br-lan: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 ...
    inet 192.168.1.1/24 brd 192.168.1.255 scope global br-lan
```

`ubus call network.interface.lan status`
reported the interface `"up": true`.
The generator, the file, the running
network: three descriptions of one
thing, all agreeing — which is UCI's
whole claim, now observed rather than
asserted.
