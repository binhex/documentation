# Gluetun Guide

## Common container settings

| Environment Variable | Value | Description |
|----------------------|-------|-------------|
| `FIREWALL_OUTBOUND_SUBNETS` | `<CIDR for your network>` | Same as `LAN_NETWORK` for the existing binhex *VPN containers |
| `VPN_PORT_FORWARDING` | `on` | Forces port forwarding |
| `PORT_FORWARD_ONLY` | `on` | Enforce port forward enabled endpoints only (recommended) |

## PIA container settings

| Environment Variable | Value | Description |
|----------------------|-------|-------------|
| `VPN_SERVICE_PROVIDER` | `PIA` | VPN provider |
| `VPN_TYPE` | `openvpn` | Gluetun VPN type for PIA is openvpn only |
| `OPENVPN_USER` | `<your PIA username>` | This will be in the format piaxxxxxxx |
| `OPENVPN_PASSWORD` | `<your PIA password>` | Your PIA password |
| `SERVER_HOSTNAMES` | `<port forward enabled endpoints>` | List of port forward enabled endpoints [here](https://gist.github.com/binhex/0d8bc1974bad90f8a61d2d4219ef862c) |

## ProtonVPN container settings

| Environment Variable | Value | Description |
|----------------------|-------|-------------|
| `VPN_SERVICE_PROVIDER` | `protonvpn` | VPN provider |
| `VPN_TYPE` | `wireguard` | ProtonVPN supports openvpn and wireguard, (wireguard recommended) |
| `WIREGUARD_PRIVATE_KEY` | - | Copied from generated Proton connection file |
| `WIREGUARD_PUBLIC_KEY` | - | Copied from generated Proton connection file |
| `SERVER_COUNTRIES` | `<select or leave empty>` | Select country or leave blank |
| `SERVER_CITIES` | `<select or leave empty>` | Select city or leave blank |

## AirVPN settings

| Environment Variable | Value | Description |
|----------------------|-------|-------------|
| `VPN_SERVICE_PROVIDER` | `airvpn` | VPN provider |
| `VPN_TYPE` | `wireguard` | AirVPN supports openvpn and wireguard, (wireguard recommended) |
