# October 2026 — firewall test screenshots

**Status: six cropped screenshot images published and linked below.** These are excerpts from the owner's original dated screenshots taken in an isolated Hyper-V / OPNsense training lab. Original full-size screenshots remain owner-held; some additional screenshots referenced in the validation report have not been published.

See the [dated validation record](../../docs/validation-2026-10.md) for topology, expected outcomes, rule attribution and limitations.

| Published image | What it documents |
| --- | --- |
| [`01-icmp-control-to-dmz-4-replies[1].png`](./01-icmp-control-to-dmz-4-replies%5B1%5D.png) | 26 October · Four successful IPv4 ICMP replies from CLIENT01 to OT-DMZ01 |
| [`02-icmp-dmz-to-control-firewall-blocks[1].png`](./02-icmp-dmz-to-control-firewall-blocks%5B1%5D.png) | 26 October · OTDMZ ingress log shows new DMZ-initiated ICMP requests blocked |
| [`03-tcp-8443-before-allow-firewall-blocks[1].png`](./03-tcp-8443-before-allow-firewall-blocks%5B1%5D.png) | 30 October · LAN ingress logs show TCP/8443 packets blocked before the scoped rule |
| [`04-tcp-8443-client-test-success[1].png`](./04-tcp-8443-client-test-success%5B1%5D.png) | 30 October · `Test-NetConnection` shows `TcpTestSucceeded: True` |
| [`05-tcp-8443-block-and-allow-logs[1].png`](./05-tcp-8443-block-and-allow-logs%5B1%5D.png) | 30 October · Ingress logs link TCP/8443 blocks and later passes to rules (historical CLIENT02 label typo) |
| [`06-config-copy-size-and-sha256-match[1].png`](./06-config-copy-size-and-sha256-match%5B1%5D.png) | 30 October · 61,360-byte off-VM file copy and SHA-256 equality result |

**Interpretation:** The images document specific endpoint outputs and firewall log events, not comprehensive network segmentation, industrial-protocol protection, a restored configuration or independent peer review. The recorded screenshots contain a historical rule-label typo, `CLIENT02`, where the traffic source `10.10.10.11` is CLIENT01; the original results are not altered. Private lab IPs, technical paths and checksum values may remain visible as evidence.

**Do not upload** OPNsense configuration XML exports (which may contain secrets), credentials, real personal information or other sensitive files. Keep originals unmodified and document test conditions and missing checks.
