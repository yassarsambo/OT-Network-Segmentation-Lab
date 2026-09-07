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

## Firewall Policy

A controlled asymmetric communication policy was implemented.

### OT DMZ → OT Control

Traffic originating from the OT DMZ and targeting the OT Control network is blocked.

Example policy:

`OTDMZ network → LAN network → BLOCK`

Firewall logging was enabled so denied traffic could be observed and investigated.

### OT Control → OT DMZ

ICMP traffic from the OT Control network to the OT DMZ was explicitly permitted for connectivity testing.

Example policy:

`LAN network → OTDMZ network → PASS ICMP`

This demonstrates how communication between OT security zones can be restricted to explicitly authorised traffic rather than allowing unrestricted connectivity.

## Validation

Firewall behaviour was validated using ICMP and TCP connectivity tests.

### DMZ to OT Control

Tests originating from the OT DMZ toward the OT Control host were unsuccessful as expected.

The OPNsense firewall logs confirmed that the traffic was blocked by the configured rule:

`Block OTDMZ to OT Control`

TCP connectivity testing to port 445 was also blocked.

### OT Control to OT DMZ

ICMP communication from the OT Control host to the DMZ host was successfully permitted.

The firewall logs recorded the traffic against the configured allow rule, confirming that the intended asymmetric policy was functioning correctly.

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
