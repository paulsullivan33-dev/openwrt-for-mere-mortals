# Network and Firewall Shape

**Outcome: you can sketch a stock
OpenWrt's network from its files alone
— which interfaces exist, who serves
addresses, and exactly what the
firewall lets through before you
change a thing.**

A fresh OpenWrt has a shape, and it is
the same shape almost everywhere,
because it comes from the files
chapters 6 read. This chapter assembles
them into the picture.

## The two-sided machine

The generated `network` file (chapter
6) gives the machine two faces:

- **lan** — the inside. On a typical
  router its ports and the Wi-Fi radios
  are joined in a bridge, `br-lan`, so
  everything plugged in or associated
  is one network. The router's own
  address is **192.168.1.1/24**, static.
  On the lab's generic x86 image the
  LAN device is simply `eth0`.
- **wan** — the outside. One port (or
  `eth1` on x86), a **DHCP client** by
  default, speaking IPv4 and IPv6
  (`wan6` rides alongside it). Whatever
  the upstream hands it — a cable
  modem, an ONT, another router — is
  accepted and used.

**netifd** owns this layer: it watches
links, runs the DHCP clients, applies
addresses, and re-does it all live
when a cable moves. Nothing in a stock
system routes between the two faces
except through the firewall's say-so.

On the LAN, **dnsmasq** (the shipped
`dhcp` file) offers addresses
**.100–.249** with 12-hour leases and
answers DNS for the house, forwarding
what it cannot answer to the resolvers
the WAN side learned. Hostnames of
DHCP clients become resolvable under
the `lan` domain, which is why a new
gadget is reachable as
`gadget.lan` minutes after joining.
IPv6 on the LAN is served by
**odhcpd**, with the router advertising
itself using the prefix the WAN side
was delegated — a sentence that hides
a great deal of machinery, all of it
in the stock files for readers who
want it.

## The firewall's default answer

Transcript t03 prints
`/etc/config/firewall` complete. Its
architecture is **zones**:

```
config defaults
	option syn_flood	1
	option input		REJECT
	option output		ACCEPT
	option forward		REJECT

config zone
	option name		lan
	list   network		'lan'
	option input		ACCEPT
	option output		ACCEPT
	option forward		ACCEPT

config zone
	option name		wan
	list   network		'wan'
	list   network		'wan6'
	option input		REJECT
	option output		ACCEPT
	option forward		DROP
	option masq		1
	option mtu_fix		1

config forwarding
	option src		lan
	option dest		wan
```

Read as prose: from the **LAN**,
everything is accepted — devices may
talk to the router, to each other, and
(through the one **forwarding**) out
to the WAN, with **masquerading**
(`masq`, NAT) making their private
addresses presentable outside. From
the **WAN**, the router itself
**rejects** unsolicited input, and
unsolicited forwards are **dropped**
silently. Then come named exceptions,
each a `config rule` in the file:
DHCP renewal on the WAN port, IPv4
ping, IGMP, and the pieces IPv6
*needs* to function (router
solicitations, neighbour discovery,
DHCPv6), plus IPsec passthrough rules.
Everything else from outside is
refused, and the file's tail is a set
of commented-out examples — port
forwards, a blocked address, an
accept from one source — left there
as templates by the project.

**firewall4** compiles this file into
**nftables** rules. Two habits follow
from that. First, the file is the
truth and the rules are a product:
`fw4 check` validates a file, and on a
running system `nft list ruleset`
shows what the file became. Second,
the defaults are a considered
position, not an accident — a stock
OpenWrt presents no listening service
to the WAN at all (SSH and the web
interface bind on the LAN side of the
world in practice, because the WAN
zone refuses the packets before any
service sees them). People do open the
WAN deliberately — a forwarded port
for a game server, a VPN listener —
and each such change is one stanza in
this file, visible to anyone who reads
it, which is precisely the property
stock firmware never offered.

## What the shape leaves out

There is no separate "router mode"
switch, no guest network until you
make one (a third zone and a second
bridge — the zone model is what makes
that a ten-line change instead of a
feature request), and no traffic
shaping until packages add it. The
shape is deliberately the smallest
thing that is a safe home router:
two faces, a guarded door, and a
text file that explains the guard.
