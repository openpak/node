# Protocol: node

## What the announce says

```json
{"services":{"photon":"203.0.113.9:22002","turn":"203.0.113.9:3478","stun":"203.0.113.9:3478"},
 "report":{"cores":4,"ram_mb":7820,"disk_gb":80,"disk_ssd":true,"ipv4":"203.0.113.9","ipv6":"","port_mbps":1000}}
```

Home's Photon master hands `photon` out for rooms hosted from your country. Home's Stardew server
hands `turn`/`stun` to players from your country. A role a server does not run is left out, and
home's own is used. The report is self-declared; it decides rotation and nothing else.
