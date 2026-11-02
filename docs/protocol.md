# Protocol: node

## What the announce says

```json
{"services":{"photon":"203.0.113.9:22002","turn":"203.0.113.9:3478","stun":"203.0.113.9:3478"},
 "report":{"cores":4,"ram_mb":7820,"disk_gb":80,"disk_ssd":true,"ipv4":"203.0.113.9","ipv6":"","port_mbps":1000}}
```

Home's Photon master hands `photon` out for rooms hosted from your country. Home's Stardew server
hands `turn`/`stun` to players from your country. A role a server does not run is left out, and
home's own is used. The report is self-declared; it decides rotation and nothing else.

## What a node is

A node is the relay tier for one country: a Photon game server and a TURN server, announcing
themselves to openpak.org once a minute. Rooms hosted by people in that country are served
from it; their friends join from anywhere, because the room itself says where it lives.

Nothing else moves. Accounts, friends, links, invitations, matchmaking, the NPLN session
documents and every console-facing hostname stay at home. A node holds no durable state: if it
dies, rooms on it end and everyone re-matches elsewhere.

Every transport OpenPak serves already tells the console where to go per session:

| Transport | Who hands out the address | What a node runs |
| --- | --- | --- |
| Photon (Outbound) | Master → Game handoff, one address per room | Game role only, reporting rooms home |
| NPLN (Stardew) | the ICE server set the session hands out | STUN/TURN (Pia peer-to-peer; TURN carries it when NAT will not) |
| NEX | auth returns the secure server; gameplay is peer-to-peer | nothing |

A room is placed when it is created, by the host's profile country. Two consoles pinned to
different matchmaking servers could never see the same room, so matchmaking is one server per
title, at home, and the node is where the packets go.

## Trust

A node holds the Photon token secret and the TURN secret. That lets it serve rooms and relay
traffic for people who were sent to it, and lets a rogue node mislabel rooms towards home. It
cannot touch accounts, friends or links: the node key only enrols servers, and a server key
only announces and runs its self-test. Approval is a person flipping a switch, so nobody is
routed to a node we have not looked at. Per-node Photon secrets are the upgrade when nodes are
run by people we have not met.
