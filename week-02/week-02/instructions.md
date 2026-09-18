# Week 02 — Instructions

Step-by-step commands used for this week's network reconnaissance and traffic analysis.

## 1. Set up the evidence workspace

```bash
mkdir -p ~/SentinelForge/week02/{outputs,screenshots,notes}
cd ~/SentinelForge/week02
```

## 2. Set the target

```bash
export TARGET=10.10.10.20
echo $TARGET
date | tee outputs/session_start.txt
```

## 3. Start a Wireshark capture

1. Open Wireshark: `sudo wireshark`
2. Select the interface on the internal lab network (`eth2`, IP `10.10.10.10`)
3. Click **Start** to begin capturing

## 4. Run the full port scan

With Wireshark still capturing in the background:

```bash
sudo nmap -p- -sV -O $TARGET | tee outputs/full_port_scan.txt
```

- `-p-` — scan all 65,535 ports (not just the common/default set)
- `-sV` — detect service versions on any open ports
- `-O` — attempt OS fingerprinting
- `tee` — save the output as evidence while still displaying it live

This scan takes a while (roughly 20 minutes) since it checks every port.

## 5. Stop and save the first Wireshark capture

1. In Wireshark, click **Stop**
2. File → Save As → `outputs/nmap_full_scan_capture.pcapng`

## 6. Run a focused scan on any open ports found

The full scan found one open port (7680/tcp). Start a **new** Wireshark capture on the same interface, then run:

```bash
sudo nmap -sC -sV -p 7680 $TARGET | tee outputs/port_7680_detail.txt
```

- `-sC` — run Nmap's default scripts against the port
- `-sV` — service version detection
- `-p 7680` — scan only this specific port

## 7. Stop and save the second Wireshark capture

1. In Wireshark, click **Stop**
2. File → Save As → `outputs/port_7680_scan_capture.pcapng`

## 8. Review the capture

Open the full scan capture and filter for the scan pattern:

```bash
wireshark ~/SentinelForge/week02/outputs/nmap_full_scan_capture.pcapng
```

Filter (in the Wireshark display filter bar):
```
tcp.flags.syn == 1 and tcp.flags.ack == 0
```

This isolates the SYN packets Nmap sent to probe each port — a clean view of the scan happening at the packet level.

Screenshot saved as: `screenshots/wireshark-syn-scan-filtered.png`

## 9. Verify evidence collected

```bash
ls -la ~/SentinelForge/week02/outputs/
```

Expected files:
- `session_start.txt`
- `full_port_scan.txt`
- `nmap_full_scan_capture.pcapng`
- `port_7680_detail.txt`
- `port_7680_scan_capture.pcapng`
