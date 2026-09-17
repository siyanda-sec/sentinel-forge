# Week 01 — Lab Setup

## Objective

Build and document the initial Sentinel Forge lab environment — three VMs on an isolated internal network, with Wazuh manager installed and running on the Ubuntu Server.

---

## Tasks

- [x] Prepare Kali Linux VM
- [x] Prepare Windows 10 VM
- [x] Prepare Ubuntu Server VM (Wazuh manager)
- [x] Configure isolated internal network (`SentinelForge`)
- [x] Assign static IPs and verify connectivity
- [x] Install Wazuh manager
- [ ] Set up Wazuh indexer and dashboard
- [ ] Enroll Windows agent
- [ ] Add screenshots to evidence folder

---

## What Was Built

Three VMs running in VirtualBox, all connected over an isolated internal network named `SentinelForge`:

| Machine | Role | IP Address | OS |
|---|---|---|---|
| Kali Linux | Attacker / analyst | 10.10.10.10 | Kali Linux |
| Windows 10 | Target | 10.10.10.20 | Windows 10 |
| Ubuntu Server | Wazuh manager | 10.10.10.30 | Ubuntu Server 24.04.4 LTS |

The internal network keeps all three VMs isolated from the host machine and the internet. Ubuntu has a second NAT adapter for internet access during setup (package installs, etc.) — this adapter is not part of lab traffic.

**Screenshot:** `evidence/screenshots/week-01/virtualbox-network-config.png`

---

## Networking

### Static IP configuration

Getting static IPs working required a different approach on each machine:

**Kali Linux** — set via `nmcli` on the internal adapter:
```bash
nmcli con mod "Wired connection 1" ipv4.addresses 10.10.10.10/24
nmcli con mod "Wired connection 1" ipv4.method manual
nmcli con up "Wired connection 1"
```

**Windows 10** — set manually through adapter TCP/IPv4 properties:
- IP: `10.10.10.20`
- Subnet: `255.255.255.0`
- No gateway needed (isolated network)

**Ubuntu Server** — configured via netplan (Ubuntu Server doesn't use NetworkManager by default):

`/etc/netplan/50-cloud-init.yaml`:
```yaml
network:
  version: 2
  ethernets:
    enp0s3:        # Internal adapter — SentinelForge network
      dhcp4: no
      addresses:
        - 10.10.10.30/24
    enp0s8:        # NAT adapter — internet access only
      dhcp4: yes
```
```bash
sudo netplan apply
```

### Windows firewall rule for ICMP

Windows blocks inbound ping by default. Added a firewall rule to allow it:
```powershell
New-NetFirewallRule -DisplayName "Allow ICMPv4-In" -Protocol ICMPv4 -IcmpType 8 -Direction Inbound -Action Allow
```

### SSH access into Ubuntu

Configured NAT port forwarding on the Ubuntu VM (host port `2222` → guest port `22`) to allow SSH access from the Mac terminal:
```bash
ssh user@127.0.0.1 -p 2222
```
This made a significant difference for working with longer commands and config files — copy/paste isn't available in the VirtualBox console without Guest Additions installed.

**Screenshot:** `evidence/screenshots/week-01/ip-configs.png`

---

## Connectivity Tests

Tested all directions across the internal network:

| Source | Destination | Result |
|---|---|---|
| Kali | Windows | ✅ |
| Kali | Ubuntu | ✅ |
| Windows | Kali | ✅ |
| Windows | Ubuntu | ✅ |
| Ubuntu | Kali | ✅ |
| Ubuntu | Windows | ✅ |

**Screenshot:** `evidence/screenshots/week-01/ping-tests.png`

---

## Wazuh Manager Installation

Installed Wazuh manager **v4.14.7** on the Ubuntu VM using the manual install method (GPG key → repo → package) rather than the all-in-one script.

```bash
# Add GPG key
curl -s https://packages.wazuh.com/key/GPG-KEY-WAZUH | gpg --dearmor | sudo tee /usr/share/keyrings/wazuh.gpg > /dev/null

# Add repository
echo "deb [signed-by=/usr/share/keyrings/wazuh.gpg] https://packages.wazuh.com/4.x/apt/ stable main" | sudo tee /etc/apt/sources.list.d/wazuh.list

# Install
sudo apt update
sudo apt install wazuh-manager
sudo systemctl enable wazuh-manager
sudo systemctl start wazuh-manager
```

**Core daemons confirmed running:**

| Daemon | Purpose |
|---|---|
| `wazuh-analysisd` | Event analysis and rule matching |
| `wazuh-remoted` | Agent communication |
| `wazuh-logcollector` | Log collection |
| `wazuh-monitord` | Agent monitoring |
| `wazuh-modulesd` | Module management |
| `wazuh-authd` | Agent authentication |
| `wazuh-db` | Database management |

```bash
sudo systemctl status wazuh-manager
```

**Screenshot:** `evidence/screenshots/week-01/wazuh-service-status.png`

> **Note:** Wazuh indexer and dashboard are not yet configured — the manager is running but there is no web UI yet. This will be completed before Week 04 (SIEM & Detection).

---

## Decisions & Notes

**RAM constraint** — host machine has 8GB RAM, which isn't enough to run all three VMs comfortably at the same time. Working approach:
- Keep Ubuntu (Wazuh) + Windows running together for monitoring and log collection
- Boot Kali only when actively running attack scenarios
- Events generated before Kali is shut down still land in Wazuh — no data lost

**Sysmon** — considering installing Sysmon on the Windows VM later for richer process and network event visibility, particularly useful during the attack simulation phase (Week 05).

---

## What's Still Pending

- [ ] Wazuh indexer setup
- [ ] Wazuh dashboard setup
- [ ] Windows agent enrollment
- [ ] Screenshots added to evidence folder

---

## Evidence

```
evidence/
└── screenshots/
    └── week-01/
        
```

---

## Next Week Preview

**Week 02 — Network Reconnaissance & Traffic Analysis**
Mapping the lab network with Nmap, capturing live traffic with Wireshark, and establishing a baseline of normal network behaviour before any attacks are introduced.

---

*Sentinel Forge · Week 01 of 08*
