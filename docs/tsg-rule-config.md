# 🛠️ TSG Policy Configuration & Core Network Infrastructure Tuning

This document captures production-level infrastructure configurations, detection rule logic, and kernel-level networking adjustments for SecurityGen TSG (Telecom Security Guard).

---

## 1. TSG Detection Rule Logic (SIEM/IDS Signatures)
Custom rule patterns deployed on the TSG UI to detect and mitigate signaling threats simulated by testing engines.

### A. SS7 Threat Detection
* **Rule 1 (Calling GT Verification):** `CallingPA == 417999880000010` (Isolating suspicious Global Title origins)
* **Rule 2 (Abnormal MAP Operation):** `MessageTypeRequest == SendIMSI`
* **Rule 3 (Geographic Anomaly / Spoofing):** `Country(IMSI) == "Italy"`
* **Rule 4 (Velocity Check):** `MessageTypeRequest == SendAuthenticationInfo AND VelocityFail` (Detecting impossible speed movement tracking)

### B. Diameter Threat Detection
* **Rule 1 (S6a/Sh Command Code Tracking):** `CC == 306` (Monitoring Authentication-Information-Request / Answer anomalies)

### C. GTP Threat Detection
* **Rule 1 (Malicious GTPv1/v2 Category):** `MessageType == Cat1MessageTypeRequest`
* **Rule 2 (Target Integrity):** `DestinationIP == "10.10.10.57"`

---

## 2. Infrastructure Configuration & Performance Tuning (CLI)

### A. Network Interface Optimization & DPDK Tuning
Adjusting Low-Level Layer 2 Packet Capture interfaces within `/opt/secgen/ace` settings:
* **DPDK Device Binding:** Switched `dpdk do devbind` from `true` to `false` to match the target kernel interface model.
* **Virtual Device Activation:** Uncommmented `vdev` parameter block to allow virtual interface injection.
* **Interface Mapping:** Enabled virtual hardware capture flags (`enabled: true`) and mapped the exact interface to the primary network card card (`eno1`).
* **Packet Loop Prevention:** Configured outbound traffic capture (`txpcap`) destination target path to `"/dev/null"` to eliminate packet echoing loops during real-time mirroring.

### B. Signaling Connectivity Settings (SS7/Diameter/GTP Link Layer)

#### [SS7 Protocol Layer - M3UA/SCTP Client]
* **Link Layer:** IPSP (IP Server Process) Connection Type, SLS set to `0`.
* **M3UA Handshake:** Optimized via `wait aspup` and `wait aspac` options.
* **Addressing:** Service Indicator `3-SCCP`, Network Indicator `0-International`.
* **Routing Keys:** OPC (Originating Point Code) `500`, DPC (Destination Point Code) `50`.
* **SCTP Transport:** Mapped Local Endpoint `10.10.10.35:2905` to Remote Endpoint Gateway Interface (`10.10.10.55:2905`).

#### [Diameter Protocol Layer - RFC 6733 Server]
* **Server Setup:** Binding IP/Port `10.10.10.35:3868`
* **Identity:** Host: `ace1.epc.mnc001.mcc412.3gppnetwork.org` / Realm: `epc.mnc001.mcc412.3gppnetwork.org`
* **Neighbors/Peers:** Peer Routing mapped to Remote Infrastructure (`10.10.10.56:3868`) and Application peer (`tsg.epc.mnc005.mcc450.3gppnetwork.org`).
* **Operational Auditing:** Configured detailed debugging logs to `/var/log/ace/dia_connectivity.log` (LogLevel `10`).

#### [GTP Packet Capturing]
* **Transport:** UDP-based kernel packet capturing enabled via network interface `eth0` with zero-byte minimal packet size padding for raw signal extraction.
