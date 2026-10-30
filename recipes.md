# Recipes

**Outcome: eight small jobs, each done
the OpenWrt way, each built only from
parts the earlier chapters verified.**

A status note before the cooking:
these recipes are assembled from the
real shipped files and grammar proven
in transcripts t03, t04 and t12–t13.
They were **not executed against a
router in this lab** — the emulated
system was left in its stock state on
purpose — so treat them as reliable
instructions, honestly labelled, not
as lab results. Every one of them is
a variation on chapter 6's sentence:
edit UCI, commit, let the service
re-apply.

## 1. Set the root password, first

Before anything else, on any fresh
box, because limits chapter 14 is
blunt about the default:

```
passwd
```

In LuCI it is System → Administration.
Until this is done, anyone on the LAN
can log in as root with no password.

## 2. Make SSH key-only

From the shipped `dropbear` file
(transcript t03) — the options exist
and are merely commented out:

```
uci set dropbear.@dropbear[0].PasswordAuth='off'
uci set dropbear.@dropbear[0].RootPasswordAuth='off'
uci commit dropbear
/etc/init.d/dropbear restart
```

Add your public key first (LuCI's
SSH-Keys page, or paste into
`/etc/dropbear/authorized_keys`), and
keep one session open while testing
the new one. Locking yourself out of
your own router is a traditional part
of the learning process that this
ordering exists to skip.

## 3. Move the LAN to a new address

For joining an existing network whose
router already owns 192.168.1.1, or
for tidiness:

```
uci set network.lan.ipaddr='192.168.7.1/24'
uci commit network
/etc/init.d/network restart
```

Reconnect at the new address. The
DHCP pool follows the interface; the
firewall never mentioned the address
at all — zones, not numbers (chapter
8) — which is why this is three lines
and not thirty.

## 4. Reserve an address for a machine

A fixed lease for the printer, in the
shipped `dhcp` file's own grammar:

```
uci add dhcp host
uci set dhcp.@host[-1].name='printer'
uci set dhcp.@host[-1].mac='AA:BB:CC:DD:EE:FF'
uci set dhcp.@host[-1].ip='192.168.1.50'
uci commit dhcp
/etc/init.d/dnsmasq restart
```

(The `AA:BB…` address is a stand-in —
use the machine's real MAC.) The
machine keeps asking by DHCP and
always gets the same answer, which
survives it being renamed, reinstalled
or replaced by its warranty twin.

## 5. Let one outside port reach one inside machine

The shipped firewall file ends with
commented examples of exactly this;
the live form is:

```
uci add firewall redirect
uci set firewall.@redirect[-1].name='Forward the game server'
uci set firewall.@redirect[-1].src='wan'
uci set firewall.@redirect[-1].dest='lan'
uci set firewall.@redirect[-1].proto='tcp'
uci set firewall.@redirect[-1].src_dport='25565'
uci set firewall.@redirect[-1].dest_ip='192.168.1.50'
uci set firewall.@redirect[-1].dest_port='25565'
uci set firewall.@redirect[-1].target='DNAT'
uci commit firewall
/etc/init.d/firewall restart
```

25565 and the game server are the
worked example; the shape is the same
for any port. Read it back with
`uci show firewall` afterwards — the
legibility is the security review.

## 6. Add a package, watch the room it takes

Chapter 7's tool, on the running
system:

```
apk update
apk add tcpdump-mini
df -h /overlay
apk del tcpdump-mini
```

`df` before and after teaches the only
storage intuition that matters: the
overlay is the whole budget, packages
spend it, deleting refunds it. Choose
the `-mini`/`-small` variants where
the feed offers them; they exist
because routers are why.

## 7. A guest network, in outline

The full version is a chapter in
itself elsewhere; the shape, from
parts already met — a second bridge
device and interface in `network`, a
`wifi-iface` (chapter 9) attached to
it, a `dhcp` instance serving it, and
a firewall **zone** for it with one
forwarding: to `wan`, never to `lan`.
Ten stanzas across four files, each
one a section type this guide has
already shown you. If that sentence
reads as a plan rather than a threat,
the guide has done its job.

## 8. The pre-upgrade ritual

Before any upgrade (chapter 11):

```
sysupgrade -l
sysupgrade -b /tmp/backup-$(date +%F).tar.gz
```

Download the tarball off the router,
note the current release and package
list (`apk info` on 25.12; `opkg status`
on anything older), read the release
notes for the version you are moving
*to*. Upgrades go well in proportion
to how boring this ritual was.
