# Region Node

OpenPak's Region Node.

| | |
| --- | --- |
| Network | Switch |
| Role | Relay tier for one country: a Photon game server and a TURN server, announcing to openpak.org once a minute |
| Depends on | openpak.org (announce and regions API) |
| Licence | AGPL-3.0-only |

## What it serves

| Service | What |
| --- | --- |
| Photon (Outbound) | Game role: rooms hosted from this country, UDP 22002 and 22012 |
| STUN/TURN (NPLN, Stardew) | Relays traffic when NAT will not allow a direct path, UDP 3478 and 49152–65535 |

## Run

```sh
cp example.env .env   # fill in; one SERVER_NAME per server
podman compose up -d
```

## Docs

- [Protocol](docs/protocol.md)
- [Evidence](docs/evidence.md)
- [Deploy](docs/deploy.md)
- [Notes](docs/notes.md)
- [Docs index](docs/README.md)

## Licence

AGPL-3.0-only. See [LICENSE](LICENSE).
