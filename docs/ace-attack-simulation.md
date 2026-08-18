# 🧪 Telecom Vulnerability Assessment via SecurityGen ACE (Simulation)

This document details the configuration and methodology used for telecom signaling vulnerability assessments across GTP, SS7, and Diameter protocols using the SecurityGen ACE (Attack Simulation Engine).

---

## 1. GTP (GPRS Tunneling Protocol) Assessment
Simulating core network infrastructure exposure and location tracking vulnerabilities.

### A. Input Configurations (Subscriber & Infrastructure)
* **Subscriber-Common:**
  * MSISDN: `821012345678` (Test MSISDN Format)
  * IMSI: `450011234567890` (Test IMSI Format)
* **Infrastructure-GTP:**
  * Networks / Segments: `10.10.10.0/24`
  * SGSN / SGW / MME IPs: `10.10.10.0/24`
  * GGSN / PGW IPs: `10.10.10.0/24`
  * Target Destination IP: `10.10.10.57`

### B. Attack Method Selection & Provisioning
* **Method:** `GTPv1 SGSN Context Request Test`
* **Target Destination IP:** `10.10.10.57`
* **Control Plane F-TEID IP:** `10.10.10.35`
* **GTP Source Compliance:** Cross-referenced MCC/MNC profile boundaries via *User Guide -> Inspection Ranges* mapping to match specific domestic Mobile Network Operator (MNO) criteria (e.g., MCC `412`, MNC `01`).

---

## 2. SS7 (Signaling System No. 7) Assessment
Evaluating subscriber data exposure and routing spoofing.

### A. Input Configurations
* **Common Identifier:** MSISDN (`821012345678`) / IMSI (`450066123456789`)
* **Scanner Node (SCCP):** Calling GT `417999880000010`

### B. Attack Method Selection
* **SendIMSI:** Exploiting MAP (Mobile Application Part) procedures to retrieve subscriber IMSI via target MSISDN.
* **SendRoutingInfoForLCS:** Simulating Location Services (LCS) routing queries to test subscriber geographical location privacy.

---

## 3. Diameter (4G/5G Signaling) Assessment
Testing S6a/Sh interface exposure for user privacy leakages.

### A. Core Inputs (Infrastructure Profile)
* **Origin Identifiers:** MCC `450` / MNC `05`
* **Destination Identifiers:** MCC `450` / MNC `05`
* **Network Entity FQDN Prefixes:** MME (`m1`), HSS (`h1`), AS (`a1`)

### B. Attack Method Selection
* **Network Info Disclosure via S6a AIR (Authentication-Information-Request):** Probing HSS data disclosure capabilities.
* **IMSI Disclosure via Sh UDR (User-Data-Request):** Exploiting Application Server (AS) interfaces to extract IMSI records via known MSISDN.
