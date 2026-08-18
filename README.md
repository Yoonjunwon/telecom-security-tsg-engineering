# 📡 Telecom Security Engineering & TSG Technical Guide

This repository contains practical technical knowledge and operational guidelines accumulated while handling **SecurityGen TSG (Telecom Security Guard)** at eSeqValley.
*(※ To comply with company security policies, all sensitive data including client IPs, internal credentials, and vendor-proprietary information have been completely removed or masked.)*

## 🎯 Core Technical Focus
* **Telecom Core Net:** Analyzing packet mirroring environments and signaling traffic within 5G/LTE core networks.
* **Protocol Analysis:** Tracking error logs and diagnosing exceptional cases for GTPv2, Diameter, and SS7 protocols.
* **OS/Infra/Web:** Troubleshooting Linux-based security solutions and hardening Nginx web servers.

---

## 🛠️ Operational Guides & Troubleshooting
*Detailed command lines and configuration steps are isolated in the [TSG Operational Runbook](./docs/tsg-runbook.md).*

### 1. Service Daemon Management
* Controls and monitors the Diameter firewall (FW) and network interface services using Linux system commands when signaling traffic anomalies occur or solution daemons fail to run properly.

### 2. Nginx Web Server Hardening (`server_tokens off`)
* Identifies and mitigates information disclosure vulnerabilities within the solution's internal web interfaces and proxy setups.
* Disabled Nginx version exposure in error pages and HTTP response headers to block potential targeted CVE attacks.

---

## 🤝 [Organizational Contribution] Enhancing Legacy Solution (GateONE) Onboarding
* **Context:** The onboarding process for GateONE (the company's core systems access control solution) lacked formal documentation, relying entirely on verbal knowledge transfers. This gap caused newly joined engineers to spend an average of 6 months undergoing trial-and-error before handling tasks independently.
* **Action:** To prevent team resource drain, I took the initiative to gather recurring queries, frequent error log patterns, and infrastructure-specific CLI commands, structuring them into a standardized technical runbook.
* **Impact:** Established a self-service documentation environment where new hires can diagnose issues independently before escalating to seniors. This reduced repetitive basic queries, minimized interruptions to senior engineering workflows, and accelerated the overall onboarding timeline.


## 📁 Detailed Engineering Handbooks (Internal Deep-Dive)
* [ACE Telecom Vulnerability Attack Simulation Guide](./docs/ace-attack-simulation.md)
* [TSG Firewalls Rule Config & Network Infrastructure Tuning](./docs/tsg-rule-config.md)



