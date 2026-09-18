# Sentinel Forge

A hands-on cybersecurity lab designed to develop practical skills in security monitoring, threat detection, investigation, and incident response.

## About

Sentinel Forge is a personal cybersecurity lab project built to bridge the gap between theory and practice. The lab uses controlled environments to simulate real security events — from network reconnaissance through to full incident investigation — and documents the process and findings at every stage.

The project covers the full SOC analyst workflow: building a lab, monitoring a network, configuring a SIEM, simulating attacks, responding to incidents, and automating security tasks.

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                 VirtualBox Internal Network                 │
│                      "SentinelForge"                        │
│                     10.10.10.0/24                            │
│                                                               │
│   ┌───────────────┐   ┌───────────────┐   ┌───────────────┐ │
│   │  Kali Linux   │   │  Windows 10   │   │ Ubuntu Server │ │
│   │  Attacker /   │◄─►│    Target     │◄─►│  SIEM (Wazuh) │ │
│   │   Analyst     │   │               │   │    Manager    │ │
│   │ 10.10.10.10   │   │ 10.10.10.20   │   │ 10.10.10.30   │ │
│   └───────────────┘   └───────────────┘   └───────┬───────┘ │
│                                                    │         │
└────────────────────────────────────────────────────┼─────────┘
                                                      │ NAT
                                                      ▼
                                                Internet (updates,
                                                package installs)
```

All three machines run in Oracle VirtualBox on a single isolated internal network. Traffic between Kali, Windows, and Ubuntu is fully contained — nothing reaches the host or the internet except through Ubuntu's separate NAT adapter, used only for package installation.

| Machine | Role | IP |
|---|---|---|
| Kali Linux | Attacker / analyst | `10.10.10.10` |
| Windows 10 | Target | `10.10.10.20` |
| Ubuntu Server | SIEM (Wazuh manager) | `10.10.10.30` |

## Objectives

- Build and maintain a small cybersecurity lab environment
- Practice network reconnaissance and traffic analysis
- Collect and analyse security logs from Windows and Linux systems
- Configure and use a SIEM (Wazuh) for centralised monitoring
- Develop and test custom security detection rules
- Simulate attacks and investigate the resulting evidence
- Conduct structured incident response exercises
- Document findings and evidence in a professional format
- Develop basic security automation using Python and Bash

## Lab Environment

| Tool | Purpose |
|---|---|
| Kali Linux | Attacker / analysis machine |
| Ubuntu Server | Target — Linux log source |
| Windows | Target — Windows event log source |
| Wazuh | SIEM — log aggregation, alerting, detection |
| Wireshark | Packet capture and traffic analysis |
| Nmap | Network scanning and enumeration |
| Python | Security automation and log parsing |
| Bash / PowerShell | Scripting and system administration |

## Project Structure

```
sentinel-forge/
│
├── week-01/          # Lab setup and environment configuration
├── week-02/          # Network reconnaissance and traffic analysis
├── week-03/          # Windows security logs and event analysis
├── week-04/          # SIEM configuration and detection rules
├── week-05/          # Attack simulation and MITRE ATT&CK mapping
├── week-06/          # Incident response exercise
├── week-07/          # Security automation (Python + Bash scripts)
├── week-08/          # Final incident investigation
│
└── evidence/
    ├── screenshots/  # Lab screenshots organised by week
    └── reports/      # Formal investigation reports
```

## Progress

| Week | Focus | Status |
|---|---|---|
| 01 | Lab setup | ✅ Complete |
| 02 | Network reconnaissance & traffic analysis | 🔄 In progress |
| 03 | Windows security logs | ⏳ Upcoming |
| 04 | SIEM & detection | ⏳ Upcoming |
| 05 | Attack simulation | ⏳ Upcoming |
| 06 | Incident response | ⏳ Upcoming |
| 07 | Security automation | ⏳ Upcoming |
| 08 | Final incident investigation | ⏳ Upcoming |

## Focus Areas

- **SOC operations** — monitoring, triage, and alert investigation
- **Security monitoring** — centralised log collection and SIEM configuration
- **Digital forensics** — evidence collection, preservation, and analysis
- **Network security** — traffic analysis, protocol inspection, and anomaly detection
- **Incident response** — structured IR lifecycle following NIST SP 800-61
- **Security automation** — scripting to reduce manual effort and increase efficiency

## Status

In progress. This repository is updated weekly as the lab develops. Each week folder contains a README documenting the objectives, methodology, findings, and evidence for that stage of the project.

---

Built by Siyanda Mzozoyana · Cybersecurity Student
