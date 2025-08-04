# Gluetun Guide

## Common settings

## PIA specific settings

| Environment Variable | Value | Description |
|----------------------|-------|-------------|
| `VPN_SERVICE_PROVIDER` | `PIA` | VPN provider |
| `VPN_TYPE` | `openvpn` | Gluetun VPN type for PIA is openvpn only |
| `OPENVPN_USER` | `<your PIA username>` | This will be in the format piaxxxxxxx |
| `OPENVPN_PASSWORD` | `<your PIA password>` | Your PIA password |
| `SERVER_HOSTNAMES` | `<port forward enabled endpoints>` | List available on Gist [here](https://gist.github.com/binhex/0d8bc1974bad90f8a61d2d4219ef862c) |
| `VPN_PORT_FORWARDING` | `on` | Forces port forwarding |
| `FIREWALL_OUTBOUND_SUBNETS` | `<CIDR for your network>` | Same as `LAN_NETWORK` for the existing binhex *VPN containers |

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
