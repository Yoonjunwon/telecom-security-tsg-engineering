# 🛠️ Signaling Firewall Policy Mapping (GSMA FASG Compliance)

Based on GSMA FS.11 (SS7 Security) and FS.19 (Diameter Security) guidelines, I analyzed and categorized signaling firewall mitigation policies for core network protection.

### 1. Category 1: Interconnect Filtering (Unauthorized Message Types)
* Disallowed specific MAP/CAP management and inquiry messages originating from external transit networks.
* **Mitigation Focus:** Dropping unapproved routing info requests (`SendRoutingInfo`, `SendIMSI`) at the boundary to prevent network topology disclosure.

### 2. Category 2: Home Subscriber Protection (Spoofing & Leakage)
* Deployed rules to block illegal inquiries targeted at home subscribers (`MyIMSI`) or home routing nodes (`MyGT`).
* **Mitigation Focus:** Dropping spoofed HLR/HSS profile modification commands (`CancelLocation`, `InsertSubscriberData`) when initiated from untrusted global titles.

### 3. Category 3: State-Aware Filtering (Location & Velocity Anomalies)
* Applied multi-protocol state correlation rules to detect structural inconsistencies based on subscriber location state databases.
* **Location Plausibility:** Validating whether the Originating Country of the incoming signaling node aligns with the subscriber's last known real-time location network.
* **Velocity Failure Verification:** Detecting physically impossible subscriber movement profiles (e.g., consecutive signaling requests from different countries within an unrealistic timeframe).
