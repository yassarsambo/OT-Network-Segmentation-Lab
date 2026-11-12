# October 2026 lab validation record

**Scope:** Private, isolated Microsoft Hyper-V / OPNsense training environment.  
**Status:** Owner-recorded tests from 26 and 30 October 2026, supported by dated local screenshots and notes. **The October screenshots and firewall configuration are not published in this repository.** This document is a test record, not an independently reproduced verification report or an OT/ICS certification.

## Tested topology

| Role | Endpoint or interface | IPv4 (recorded at time of testing) |
| --- | --- | --- |
| OT Control test client | `CLIENT01`, Windows 11 | `10.10.10.11/24` |
| OT DMZ test endpoint | `OT-DMZ01`, Windows | `10.10.20.100/24` |
| OPNsense LAN ingress | `OT-FW01`, Hyper-V switch `OT-Control` | `10.10.10.1/24` |
| OPNsense OTDMZ ingress | `OT-FW01`, Hyper-V switch `OT-DMZ` | `10.10.20.1/24` |

OPNsense displayed version **26.7-amd64** during the 25 October interface review. Interface settings and DHCP-assigned addresses should be reconfirmed before another test. The earlier September repository screenshots used a different control-zone endpoint, `OT-HMI01 (10.10.10.50)`; do not treat those as screenshots of the October CLIENT01 tests. No genuine PLC, SCADA service or industrial HMI was deployed.

## 26 October: IPv4 ICMP

| Test | Source → destination | Recorded endpoint outcome | Matching ingress firewall evidence |
| --- | --- | --- | --- |
| ICMP-01, allowed | `CLIENT01 10.10.10.11` → `OT-DMZ01 10.10.20.100` | 4 replies / 4 sent, 0% loss | LAN / In / **pass** at 18:53:19, rule `Allow OT Control to OT DMZ - Lab` |
| ICMP-02, unsolicited reverse attempt | `OT-DMZ01 10.10.20.100` → `CLIENT01 10.10.10.11` | 0 replies / 4 sent, four timeouts | OTDMZ / In / **block** at 18:36–18:37 and 18:43, rule `Block OTDMZ to OT Control` |

A stateful firewall may permit response packets to an already authorised flow while blocking *new* reverse-initiated requests. The positive forward ping and negative reverse ping are separate observations; neither establishes an all-protocol isolation guarantee. The existing ICMP allowance was limited to IPv4 Echo Request on the LAN interface.

### Reproduction check (authorised lab only)

On the Control client: `ping -n 4 10.10.20.100`.  
On the DMZ endpoint: `ping -n 4 10.10.10.11`.  
Correlate endpoint results with timestamps, direction, action, source, destination and named ingress rules in OPNsense Live View. Inspect firewall and endpoint state before assuming that a timeout was caused by OPNsense.

## 30 October: scoped IPv4 TCP/8443

A temporary listener was started on `OT-DMZ01` at `10.10.20.100:8443`, and a narrow inbound Windows Firewall allowance permitted the Control client's address. The host firewall was not disabled globally. The listener was confirmed in `Listen` state.

| Test | Client command/flow | Recorded result | Matching OPNsense evidence |
| --- | --- | --- | --- |
| TCP-01 before OPNsense allowance | `CLIENT01` → `10.10.20.100:8443` using `Test-NetConnection` | `PingSucceeded=True`; `TcpTestSucceeded=False` | LAN / In / **block** entries around 17:10, labelled `Default deny / state violation rule` |
| TCP-02 after narrow allowance | Same endpoints and TCP port | `TcpTestSucceeded=True` | LAN / In / **pass** at 18:28:13, named TCP allow rule |
| TCP-03 subsequent logged flow | Same endpoints and TCP port | No separate endpoint-output screenshot retained for this repeat | LAN / In / **pass** at 18:32:23, same named rule |

The new OPNsense rule was **LAN / In / Pass / IPv4 TCP**, source `10.10.10.11`, destination `10.10.20.100`, destination port `8443`, logging enabled. Before the successful test, the rule was assigned to the LAN interface; a preliminary incomplete floating-rule configuration was **not** the tested policy.

**Historical labelling caveat:** At the moment the passing logs were captured, the rule label mistakenly said `Allow CLIENT02 to OT DMZ TCP 8443 - Lab`. The source IP was CLIENT01 (`10.10.10.11`). The rule name was corrected to `Allow CLIENT01 to OT DMZ TCP 8443 - Lab` after testing. Do not interpret that label typo as a third test client.

The default-deny log label does not itself prove that a state violation occurred. The successful TCP test demonstrates reachability to a **temporary TCP listener only**, not HTTPS, a production service, OPC UA, Modbus or another industrial protocol.

### Cleanup and evidence boundary

- The temporary listener's `Stop()` and removal of the endpoint's temporary Windows Firewall rule returned without displayed errors.
- The added OPNsense LAN TCP/8443 rule was retained **disabled**, as shown in the final 18:49 rule-list screenshot.
- No independent post-cleanup listener query, host-firewall rule readback or fresh packet test was captured. Therefore, cleanup is **not** described as independently revalidated.

## 30 October: configuration export and copy-integrity check

After the TCP rule was disabled, an OPNsense configuration export was saved within CLIENT01. A copy was moved to the physical Hyper-V host using PowerShell Direct. The displayed files were each **61,360 bytes**, and SHA-256 hashes calculated on both sides compared equal (`True`). The PowerShell session was closed without displayed errors.

This establishes a matching **off-VM copy**, not verified encryption, a separate physical-media backup, successful decryption or configuration restoration. The configuration file itself is **not** published because firewall exports may contain confidential settings and secrets.

## Existing public evidence vs local evidence

The repository's `screenshots/` directory currently contains September screenshots of the architecture, rules and early tests. Those are historical, and are **not** a public copy of the 26/30 October proof.

The dated local evidence index includes:

- 26 October: `Screenshot 2026-10-26 183715.png` (reverse ICMP timeouts), `184349.png` (named OTDMZ block log), `185335.png` (forward ICMP replies), `185457.png` (named LAN allow log).
- 30 October: `Screenshot 2026-10-30 171129.png` (initial TCP failure), `172401.png` (LAN ingress block), `182849.png` (successful TCP result), `183730.png` (matching pass logs), `184929.png` (test rule disabled), `201523.png` (matching copy hashes).

These filenames identify evidence retained in the owner's private project records; **the files have not been uploaded here**. Before publication, inspect each for personal data, login identifiers, configuration secrets and unrelated windows, and redact appropriately. Do not fabricate or replace original results.

## Not yet demonstrated

- Blocking of new reverse-direction TCP/8443 sessions, comprehensive IPv4 policy coverage or all-host/all-port isolation.
- IPv6 isolation: an enabled broad LAN IPv6 rule was observed and still requires review.
- DMZ DNS policy: blocked UDP traffic to the firewall's DNS port was observed; whether it should be permitted is unresolved.
- Validated UDP controls, full network failure handling, IDS/IPS detection, Suricata outcomes or industrial protocol traffic.
- OPNsense backup restore/decryption or tested disaster recovery.
- Independent peer validation, operational deployment, adoption or measurable impact.

## Repeatable evidence checklist

For each future test, retain: **date/time and timezone · VM/network baselines · exact test direction and port · expected outcome · endpoint output · matching OPNsense ingress log and named rule · rule state before/after · cleanup readback · sensitivity review before sharing**.

Any new work should occur only on authorised, nonproduction equipment.