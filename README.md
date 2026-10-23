# Running a region node

A node is the relay tier for one country: a Photon game server and a TURN server, announcing
themselves to openpak.org once a minute. Rooms hosted by people in that country are served
from it; their friends join from anywhere, because the room itself says where it lives.

Nothing else moves. Accounts, friends, links, invitations, matchmaking, the NPLN session
documents and every console-facing hostname stay at home. A node holds no durable state: if it
dies, rooms on it end and everyone re-matches elsewhere.

## Why a node is only relays

Every transport OpenPak serves already tells the console where to go **per session**:

| Transport | Who hands out the address | What a node runs |
| --- | --- | --- |
| Photon (Outbound) | Master → Game handoff, one address per room | Game role only, reporting rooms home |
| NPLN (Stardew) | the ICE server set the session hands out | STUN/TURN (the game is Pia peer-to-peer; TURN carries it when NAT will not) |
| NEX | auth returns the secure server; gameplay is peer-to-peer | nothing |

A room is placed when it is created, by the host's profile country. Two consoles pinned to
different matchmaking servers could never see the same room, so matchmaking is one server per
title, at home, and the node is where the packets go.

## Bring-up

1. Apply from your account page on openpak.org (Account › Run a region node) for a location
   OpenPak lists as wanted, describing the servers you plan to run. The minimums and the
   operator terms are at `https://openpak.org/operators`.
2. Once an admin approves the application, they send you the node slug, the node key (shown
   once) and the two shared secrets in `example.env`.
3. On each server: `cp example.env .env`, fill it in (a different `SERVER_NAME` per server),
   `podman compose up -d`. The first start joins the server with the node key and keeps its own
   server key; revoking one server does not touch the others.
4. Open the firewall: `22002/udp`, `22012/udp` (Photon), `3478/udp` and `49152–65535/udp` (TURN).
   Nothing TCP needs to be reachable from outside.
5. Every server announces once a minute and runs one bandwidth self-test a day against
   openpak.org (home measures it). The admin approves the node once all its servers report and
   meet the minimum; from then on `https://openpak.org/api/v1/regions` lists it while it stays
   compliant. A server that stops reporting or drops below the minimum takes the node out of
   rotation until it is back; approval is kept.

## What the announce says

```json
{"services":{"photon":"203.0.113.9:22002","turn":"203.0.113.9:3478","stun":"203.0.113.9:3478"},
 "report":{"cores":4,"ram_mb":7820,"disk_gb":80,"disk_ssd":true,"ipv4":"203.0.113.9","ipv6":"","port_mbps":1000}}
```

Home's Photon master hands `photon` out for rooms hosted from your country. Home's Stardew server
hands `turn`/`stun` to players from your country. A role a server does not run is left out, and
home's own is used. The report is self-declared; it decides rotation and nothing else.

## At home (for whoever runs openpak.org)

- Photon's HTTP port (8080: `/health`, `/players`, and now `POST /internal/node-room`) must be
  reachable by nodes, through Traefik with TLS as `photon.openpak.org`. Reports carry the shared
  Photon secret; the health routes stay public.
- Home's Photon gets `OPENPAK_Federation__RegionsUrl=https://openpak.org/api/v1/regions`, and
  reaches nx-baas (`OPENPAK_BAAS_URL`) to learn a player's country.
- Home's Stardew gets `OPENPAK_REGIONS_URL=https://openpak.org/api/v1/regions`.
- The website needs nothing new; `/api/v1/regions` and the announce route are in the build.

## Trust

A node holds the Photon token secret and the TURN secret. That lets it serve rooms and relay
traffic for people who were sent to it, and lets a rogue node mislabel rooms towards home. It
cannot touch accounts, friends or links: the node key only enrols servers, and a server key only announces and runs its self-test.
Approval is a person flipping a switch, so nobody is routed to a node we have not looked at.
Per-node Photon secrets are the upgrade when nodes are run by people we have not met.

## Coming: OpenPak Node OS

The compose recipe above is how a node runs today, and it stays until its replacement ships.
The planned replacement is OpenPak Node OS: Fedora CoreOS with the node's services as podman
quadlets, managed by a signed node agent. The agent is not built yet; nothing here depends on it.

## License

AGPL-3.0-only. See [LICENSE](LICENSE).
