# Week 1 — Lab Setup

## Objective

Build and document the initial Sentinel Forge lab environment.

## Tasks

- [x] Prepare Kali Linux
- [x] Prepare Windows VM
- [x] Prepare Ubuntu Server (Wazuh manager)
- [x] Configure isolated lab network
- [x] Verify connectivity
- [x] Document the lab architecture
- [X] Capture initial screenshots

## What I built

Three VMs in VirtualBox, all talking to each other over an isolated internal network called `SentinelForge`:

- **Kali Linux** — my attacker/analyst box — 10.10.10.10
- **Windows 10** — the target machine —  10.10.10.20
- **Ubuntu Server 24.04.4 LTS** — running the Wazuh manager, hostname `sentinelforge-wazuh` — 10.10.10.30

The internal network keeps all three isolated from my host machine and the internet. Ubuntu also has a second NAT adapter just for internet access during setup (installing packages, etc.) — that adapter isn't part of the lab traffic.

## Networking notes

Getting the static IPs set up took a bit of trial and error:

- Kali: static IP set via `nmcli` on the internal adapter
- Windows: set manually through the adapter's TCP/IPv4 properties
- Ubuntu: set via netplan (`/etc/netplan/50-cloud-init.yaml`), since Ubuntu Server doesn't use NetworkManager by default

Windows blocks inbound ping by default, so I had to add a firewall rule before Kali could reach it:
```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4 -IcmpType 8 -Direction Inbound -Action Allow
```

I also set up SSH access into the Ubuntu VM (via NAT port forwarding, host port 2222 → guest port 22) so I could work from my Mac's terminal instead of the VirtualBox console window. Made a big difference once I got into longer commands and editing config files — copy/paste just isn't available in the console without Guest Additions installed.

## Connectivity

Tested every direction, all working:

- Kali ↔ Windows ✅
- Ubuntu ↔ Kali ✅
- Ubuntu ↔ Windows ✅



## Wazuh manager

Installed Wazuh manager (v4.14.7) on the Ubuntu VM using the manual install steps (GPG key → repo → package) rather than the all-in-one script. Service is enabled and running, all core daemons up (analysisd, remoted, logcollector, monitord, modulesd, authd, wazuh-db).

Indexer and dashboard aren't set up yet — that's next, before I can actually see anything in a web UI or enroll the Windows agent.

## Decisions

- Might add Sysmon on Windows later for better visibility once I get into the attack simulation phase.
- My host only has 8GB RAM, so I can't run all three VMs at once comfortably. Plan is to keep Wazuh + Windows running together, and only boot Kali when I'm actually running an attack — the events still land in Wazuh even after Kali is shut back down.

## Evidence

Screenshots (VirtualBox network settings, IP configs, ping tests, Wazuh service status) live in the repo's `evidence/` and `screenshots/` folders.

## Status

In progress — architecture, networking, and Wazuh manager are done. Still need to add screenshots and get the indexer/dashboard running.
