# Tailscale on Technicolor gateways

Installer for Tailscale on Technicolor / Telstra gateways running firmware **20.3.c** and newer (OpenWrt-based, TUN required).

Based on [UncleSam1966/tch-tailscale](https://github.com/UncleSam1966/tch-tailscale).

## Install on the modem

SSH in as root, then:

```sh
curl -skLo tailscale-setup https://raw.githubusercontent.com/kavie87/tch-tailscale/main/tailscale-setup
chmod +x tailscale-setup
```

### Default (LAN GUI only)

Tailscale comes up as a subnet router and exit node. The web GUI stays on `http://192.168.0.1` and is **not** reachable at the Tailscale `100.x` address.

```sh
./tailscale-setup
```

Unattended:

```sh
./tailscale-setup -y
```

The first install also schedules a Tailscale upgrade for Saturday morning. `-c` turns that schedule off or on.

### GUI over Tailscale

Same install, plus the Technicolor Host-check patch so `http://100.x/` opens the GUI. Only devices on your tailnet can reach that address.

Fresh install:

```sh
./tailscale-setup -g
```

Unattended:

```sh
./tailscale-setup -yg
```

Already installed, GUI not enabled yet:

```sh
./tailscale-setup -g
```

Safe to run again.

## Other options

| Flag | What it does |
|------|----------------|
| `-a alias` | Tailscale machine name (default: gateway hostname) |
| `-c` | Toggle the weekly auto-update cron job |
| `-d` | Roll back one Tailscale version |
| `-i` | Upgrade Tailscale |
| `-r` | Remove Tailscale, keep node identity |
| `-u` | Remove Tailscale completely |
| `-U` | Replace this script from GitHub |
| `-y` | Answer yes to the confirm prompt |

`-y`, `-a` and `-g` can be combined. Example: `./tailscale-setup -yg -a dja0231-main`.

After install, approve the device (and advertised subnet routes) in the [Tailscale admin console](https://login.tailscale.com/admin/machines).

## Gateway web UI card

`tch-gui-unhide-xtra.tailscale` is not run by itself. Copy it into the same directory as [tch-gui-unhide](https://github.com/seud0nym/tch-gui-unhide), then run that tool. It adds a Tailscale card when `/root/tailscale/tailscale` is already installed.

## Requirements

- Firmware 20.3.c or newer with `CONFIG_TUN=y` (`zcat /proc/config.gz | grep CONFIG_TUN`)
- Internet on the WAN
- Root SSH

Do **not** add a UCI `network.tailscale` interface or run `ifup tailscale`. That lets netifd take over `tailscale0` and strips the `100.x` address.
