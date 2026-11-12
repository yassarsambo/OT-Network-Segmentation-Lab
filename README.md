# OT Network Segmentation & Firewall Lab

An isolated, OT-inspired virtual networking lab using OPNsense and Microsoft Hyper-V to investigate firewall policy, traffic restrictions and reproducible test evidence. No production OT systems or industrial control devices were used.

**Latest documented tests:** [26–30 October 2026 validation record](docs/validation-2026-10.md), with [six cropped October evidence screenshots](screenshots/october-2026/README.md) now published. Earlier September screenshots remain available separately; the original full-size October images and firewall configuration export remain private.

## Project Objective

The objective of this project was to build a segmented OT network architecture and demonstrate how firewall controls can restrict communication between an OT DMZ and an OT Control network.

The lab follows key OT security principles including:

- Network segmentation
- Least privilege
- Controlled inter-zone communication
- Default-deny firewall behaviour
- Firewall logging and traffic validation
- Separation of OT assets from less-trusted network zones

## Lab Architecture

The environment was built using Microsoft Hyper-V and OPNsense.

| Security Zone | Subnet | Purpose |
|---|---|---|
| OT Control Network | `10.10.10.0/24` | Hosts OT control assets |
| OT DMZ | `10.10.20.0/24` | Provides an isolated intermediary network |
| OPNsense | Multi-interface firewall | Enforces communication policy between zones |

### Key Systems

- **OT-FW01** — OPNsense firewall
- **OT-HMI01** — Windows test host representing a control-zone asset (not a functioning industrial HMI); used in the earlier screenshots
- **CLIENT01** — Windows 11 test client (`10.10.10.11`) used in the October validation
- **OT-DMZ01** — Windows host located within the OT DMZ
- **Hyper-V Virtual Switches** — Provide isolated virtual network segments


### Network Architecture

The diagram below shows the segmented lab architecture, with OPNsense enforcing communication between the OT Control network and OT DMZ.

![OT Network Architecture and Validation](screenshots/00-ot-network-architecture-and-validation%5B1%5D.png)
## Firewall Policy

An asymmetric IPv4 policy was configured and evaluated for specific ICMP and TCP tests. It has **not** been established that all protocols and hosts are correctly isolated. An enabled broad IPv6 LAN allowance requires further review.
### Implemented Firewall Rules

The OPNsense firewall was configured to enforce controlled communication between the OT Control and OT DMZ security zones.

![OPNsense OT Segmentation Firewall Rules](screenshots/01-firewall-segmentation-rules%5B1%5D.png)

### OT DMZ → OT Control

Traffic originating from the OT DMZ and targeting the OT Control network is blocked.

Example policy:

`OTDMZ network → LAN network → BLOCK`

Firewall logging was enabled so denied traffic could be observed and investigated.
#### Validation — Blocked DMZ Traffic

Connectivity testing from **OT-DMZ01 (10.10.20.100)** to **OT-HMI01 (10.10.10.50)** confirmed that the firewall prevented unauthorised communication from the DMZ into the OT Control network.

The September screenshots show blocked ICMP and a historical TCP/445 connectivity attempt. A TCP/445 listener and all relevant host-firewall conditions have not been independently reconciled, so this is not presented as a conclusive validated service-level TCP/445 block.

![DMZ to OT Control TCP 445 Blocked](screenshots/02-dmz-to-control-tcp445-blocked%5B1%5D.png)

The accompanying historical firewall screenshots show denied packets under the **Block OTDMZ to OT Control** policy. The detailed October results and their named ingress-rule correlation are recorded [separately](docs/validation-2026-10.md).

![Firewall Log - DMZ to OT Control Blocked](screenshots/03-firewall-log-dmz-to-control-blocked%5B1%5D.png)

### OT Control → OT DMZ

ICMP traffic from the OT Control network to the OT DMZ was explicitly permitted for connectivity testing.

Example policy:

`LAN network → OTDMZ network → PASS ICMP`

This demonstrates how communication between OT security zones can be restricted to explicitly authorised traffic rather than allowing unrestricted connectivity.



Connectivity testing from **OT-HMI01 (10.10.10.50)** to **OT-DMZ01 (10.10.20.100)** confirmed that authorised ICMP traffic was permitted through the firewall.

![OT Control to DMZ Ping Success](screenshots/04-control-to-dmz-ping-success%5B1%5D.png)

The OPNsense firewall logs confirmed that the traffic matched the configured allow rule, demonstrating that permitted communication could pass while the reverse direction remained restricted.

![Firewall Log - OT Control to DMZ Pass](screenshots/05-firewall-log-control-to-dmz-pass%5B1%5D.png)

## October validation highlights

The 26 October lab tests used **CLIENT01 (`10.10.10.11`)** and **OT-DMZ01 (`10.10.20.100`)**, which differ from the earlier screenshots showing `OT-HMI01` (`10.10.10.50`). They recorded four successful forward IPv4 ICMP replies, four denied new reverse-direction ICMP requests, and correlating named ingress-firewall logs.

On 30 October, a temporary TCP listener on the DMZ host at port **8443** was initially unreachable from CLIENT01 and then reachable after a source/destination/port-restricted OPNsense allowance. The rule was subsequently disabled; no independent post-cleanup connection test was captured. A separate OPNsense configuration export was copied from the guest to the physical host and **SHA-256 compared equal**. No restore test or decryption check was completed.

See the [October test matrix, conditions, log attribution and limitations](docs/validation-2026-10.md) and [six published screenshot extracts](screenshots/october-2026/README.md). The original full-size October screenshots and firewall export are **not public**.

## Security Concepts Demonstrated

This project demonstrates practical understanding of:

- OT/ICS-inspired network segmentation in an isolated simulation
- Security zones and conduits
- Firewall rule configuration
- Source and destination network policies
- Least-privilege communication
- Default-deny policy investigation for tested flows (not a verified complete default-deny posture)
- ICMP and TCP traffic testing
- Firewall log analysis
- Network troubleshooting
- OT DMZ-inspired virtual network architecture
- Hyper-V virtual networking
- OPNsense firewall administration

## Technologies Used

- OPNsense
- Microsoft Hyper-V
- Windows 11
- PowerShell
- TCP/IP
- ICMP
- Virtual network switches

## Verified scope and gaps

This lab does not currently demonstrate real ICS hardware, production HMI/SCADA services, OT protocols (such as Modbus, DNP3 or OPC UA), reverse TCP blocking, comprehensive UDP or IPv6 isolation, successful recovery from backup, or independent adoption. A firewall export could contain secrets and will not be made public. For reproducibility, the current repository provides a topology, historical screenshots and the October test record, **not** a distributable working configuration or complete automated test harness.

## Next Development

Future development of the lab will include:

- More granular firewall rules
- OT protocol traffic simulation
- IDS/IPS monitoring
- Suricata deployment
- Centralised security logging
- Additional OT network zones
- Attack and detection simulations

## Purpose

This project was developed as part of my practical OT cybersecurity portfolio to complement my MSc Cyber Security and hands-on exposure to Operational Technology environments.

It documents my configuration and verification of selected controls within a simulated OT network. The tests and unverified areas are described explicitly; no production deployment, blanket segmentation assurance, novel security discovery or independent recognition is claimed.
