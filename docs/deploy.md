# Deploy: node

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

## At home (for whoever runs openpak.org)

- Photon's HTTP port (8080: `/health`, `/players`, and now `POST /internal/node-room`) must be
  reachable by nodes, through Traefik with TLS as `photon.openpak.org`. Reports carry the shared
  Photon secret; the health routes stay public.
- Home's Photon gets `OPENPAK_Federation__RegionsUrl=https://openpak.org/api/v1/regions`, and
  reaches nx-baas (`OPENPAK_BAAS_URL`) to learn a player's country.
- Home's Stardew gets `OPENPAK_REGIONS_URL=https://openpak.org/api/v1/regions`.
- The website needs nothing new; `/api/v1/regions` and the announce route are in the build.
