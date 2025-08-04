# Gluetun Guide

## Common settings

## PIA specific settings

| Environment Variable | Value |
|----------------------|-------|
| `VPN_SERVICE_PROVIDER` | `PIA` |
| `VPN_TYPE` | `openvpn` |
| `OPENVPN_USER` | `<your PIA username>` |
| `OPENVPN_PASSWORD` | `<your PIA password>` |
| `SERVER_HOSTNAMES` | `<port forward enabled endpoints>` e.g. `ca-toronto.privacy.network,nl-amsterdam.privacy.network` |
| `VPN_PORT_FORWARDING` | `on` |
| `FIREWALL_OUTBOUND_SUBNETS` | `<CIDR for your network>` (same as LAN_NETWORK for my existing VPN containers) |

## ProtonVPN settings

| Environment Variable | Value |
|----------------------|-------|
| `VPN_SERVICE_PROVIDER` | `protonvpn` |
| `VPN_TYPE` | `wireguard` |
| `WIREGUARD_PRIVATE_KEY` | `<copied from generated Proton connection file>` |
| `SERVER_COUNTRIES` | `<select or leave empty>` |
| `SERVER_CITIES` | `<select or leave empty>` |
| `VPN_PORT_FORWARDING` | `on` |
| `TZ` | `<set to my local time zone>` |
| `PORT_FORWARD_ONLY` | `on` |
| `WIREGUARD_PUBLIC_KEY` | `<copied from generated Proton connection file>` |
| `WIREGUARD_IMPLEMENTATION` | `auto` (left at default)` |
| `WIREGUARD_MTU` | `1400` (left at default) |
| `VPN_PORT_FORWARDING_STATUS_FILE` | `/gluetun/forwarded_port` (left at default) |

## AirVPN settings
