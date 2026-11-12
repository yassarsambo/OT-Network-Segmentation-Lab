# October 2026 — firewall test screenshots

**Status: screenshot image files awaiting upload.** This folder is reserved for *cropped extracts* of the six original screenshots from the owner's isolated Hyper-V/OPNsense training lab. The original images remain owner-held. Do not interpret these descriptions as meaning the images are already available here.

See the [dated validation record](../../docs/validation-2026-10.md) for topology, observed outcomes, rule attribution and limitations.

| Expected screenshot | What it documents |
| --- | --- |
| `01-icmp-control-to-dmz-4-replies.png` | 26 October: four replies from Control-zone CLIENT01 to DMZ endpoint |
| `02-icmp-dmz-to-control-firewall-blocks.png` | 26 October: named OTDMZ ingress-rule ICMP blocks |
| `03-tcp-8443-before-allow-firewall-blocks.png` | 30 October: LAN-ingress TCP/8443 blocked before scoped allowance |
| `04-tcp-8443-client-test-success.png` | 30 October: successful `Test-NetConnection` to temporary port-8443 listener |
| `05-tcp-8443-block-and-allow-logs.png` | 30 October: logs for blocked and permitted TCP flows; historical rule label typo `CLIENT02` (source `10.10.10.11` is CLIENT01) |
| `06-config-copy-size-and-sha256-match.png` | 30 October: off-VM copy size and matching SHA-256 comparison |

**Interpretation:** These are endpoint command outputs and firewall event observations, not proof of comprehensive segmentation, industrial protocol protection or validated disaster recovery. Local private IPs, technical file paths and a checksum may remain visible because they form part of the observed evidence; the images do not contain published firewall configuration exports. Do not upload OPNsense `config*.xml` files to this public repository.

Once the six crops are uploaded, update the validation record to link them. Preserve the original results and explain test limitations explicitly.
