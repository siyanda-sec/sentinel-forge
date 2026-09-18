# Week 02 — Network Reconnaissance & Traffic Analysis

## Objective

Map the Windows target's network attack surface using Nmap, and capture the scan traffic itself with Wireshark to understand what reconnaissance looks like at the packet level. This establishes a baseline to compare against during the Week 05 attack simulation.

## Tasks

- [x] Set up evidence workspace
- [x] Run a full port scan (all 65,535 ports) against the Windows target
- [x] Capture scan traffic with Wireshark
- [x] Run a focused scan on any open ports found
- [x] Analyse the packet capture for the scan pattern
- [x] Document findings

## Target

| Field | Value |
|---|---|
| Target | Windows 10 VM |
| IP | `10.10.10.20` |
| Scanning machine | Kali Linux (`10.10.10.10`) |

## Finding: Windows target exposes minimal attack surface

**Observation**

A full TCP port scan (all 65,535 ports, with service version and OS detection) against the Windows VM found only one open port:

| Port | Service | State |
|---|---|---|
| 7680/tcp | pando-pub (Windows Delivery Optimization) | Open |

All other 65,534 ports returned as **filtered** rather than closed — meaning the host firewall is actively dropping probe responses rather than passively rejecting them with a reset.

A focused follow-up scan (`-sC -sV`) against port 7680 confirmed the port is open and responsive, but didn't reveal further service detail — expected, since Delivery Optimization doesn't run a traditional banner-based protocol.

OS fingerprinting returned a 97% confidence guess of Windows 10/11, but no exact match — Nmap notes this is because reliable OS detection needs both an open and a closed port to compare against, and this host only offered one open port and a wall of filtered ones.

**Risk**

Low. A minimal open attack surface is a strong security posture — there is very little here for an external attacker to target directly. The one open service is a standard, low-risk Windows background component.

**Evidence**

- `outputs/full_port_scan.txt` — full port scan results
- `outputs/port_7680_detail.txt` — focused scan on the open port
- `outputs/nmap_full_scan_capture.pcapng` — packet capture of the full scan
- `outputs/port_7680_scan_capture.pcapng` — packet capture of the focused scan
- `screenshots/wireshark-syn-scan-filtered.png` — filtered Wireshark view showing the SYN scan pattern

**Note**

This scan establishes the Week 02 network baseline for the Windows target. It will be used for comparison during the Week 05 attack simulation — any newly opened ports or services at that point will stand out clearly against this result.

## Status

Complete. Baseline established and documented. See `instructions.md` for the exact steps and commands used.
