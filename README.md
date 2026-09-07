# OT Network Segmentation & Firewall Lab

A hands-on Operational Technology (OT) cybersecurity lab demonstrating network segmentation, firewall policy enforcement, controlled communication between security zones, and traffic validation using OPNsense and Microsoft Hyper-V.

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
- **OT-HMI01** — Windows-based simulated HMI/control asset
- **OT-DMZ01** — Windows host located within the OT DMZ
- **Hyper-V Virtual Switches** — Provide isolated virtual network segments


### Network Architecture

The diagram below shows the segmented lab architecture, with OPNsense enforcing communication between the OT Control network and OT DMZ.

![OT Network Architecture and Validation](screenshots/00-ot-network-architecture-and-validation%5B1%5D.png)
## Firewall Policy

A controlled asymmetric communication policy was implemented.
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

Both ICMP and TCP/445 connectivity were denied as expected.

![DMZ to OT Control TCP 445 Blocked](screenshots/02-dmz-to-control-tcp445-blocked%5B1%5D.png)

The OPNsense firewall logs confirmed that the blocked traffic matched the configured **Block OTDMZ to OT Control** policy.

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

## Security Concepts Demonstrated

This project demonstrates practical understanding of:

- OT/ICS network segmentation
- Security zones and conduits
- Firewall rule configuration
- Source and destination network policies
- Least-privilege communication
- Default-deny security principles
- ICMP and TCP traffic testing
- Firewall log analysis
- Network troubleshooting
- OT DMZ architecture
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

It demonstrates my ability to design, configure, test and validate security controls within a simulated OT network.
