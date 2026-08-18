# 📓 TSG Operational Guide & Command Runbook

## 1. TSG Service Management & Daemon Restart
Run the following commands sequentially to restart the firewall and network interface services when traffic detection anomalies or process crashes occur.

```bash
# Restart Diameter Firewall Service
sudo systemctl restart tsg-fw-diameter.service

# Check Status and Restart Telecom Network Interface Service
sudo systemctl status ace-net-diameter.service
sudo systemctl restart ace-net-diameter.service
```

## 2. Nginx Web Server Hardening Configuration
Step-by-step procedure to disable web interface version exposure.

```bash
# Edit Nginx Configuration File
sudo vi /etc/nginx/conf.d/redirect.conf

# [Modification] Uncomment or ensure the following line is active
server_tokens off;

# Validate Nginx configuration syntax
sudo nginx -t

# Restart Nginx and TSG target services to apply changes
sudo systemctl restart ace.target nginx
```

### ⚠️ Troubleshooting: Daemon Failure During Hardening
When enabling `server_tokens off;`, the Nginx daemon may fail to restart, throwing a systemd control process error (`failed because the control process exited with error code`).

* **Root Cause:** Accidental syntax typos or a missing trailing semicolon (`;`) inside `/etc/nginx/conf.d/redirect.conf`. Nginx strictly requires a trailing semicolon for every configuration directive; commenting it out bypasses the parser, but uncommenting it activates the syntax check, causing a process crash if invalid.
* **Best Practice:** Never bypass `sudo nginx -t`. Always ensure the test returns a `successful` flag before triggering a full service restart to prevent web server downtime.


## 3. Traffic Manipulation & Injection for Policy Validation (tcprewrite / tcpreplay)

Procedure for modifying PCAP packet attributes (L2/L3 layers) and injecting simulated telecom attacks (`SS7`, `Diameter DoS`) into dedicated VLAN interfaces to validate TSG firewall detection rules.

### A. PCAP Modification & Address Rewriting
Before replaying captured signaling attacks, rewrite the destination IP map, update the hardware Destination MAC (DMAC) address to match the receiving testing interface, and recalculate packet checksums to prevent kernel drops.

```bash
# 1. Rewrite SS7 signaling packet metadata
sudo tcprewrite --infile=/tmp/ss7_attack_source.pcap \
                --outfile=/tmp/fixed_ss7_attack.pcap \
                --dstipmap=0.0.0.0/0:10.10.10.106/32 \
                --enet-dmac=30:9c:23:f2:28:24 \
                --fixcsum

# 2. Rewrite Diameter Subscriber DoS attack packet metadata
sudo tcprewrite --infile=/tmp/diameter_dos_source.pcap \
                --outfile=/tmp/fixed_diameter_dos.pcap \
                --dstipmap=0.0.0.0/0:10.10.10.106/32 \
                --enet-dmac=30:9c:23:f2:28:24 \
                --fixcsum
```

### B. Traffic Injection & Multi-VLAN Replay
Inject the sanitized and fixed signaling packets into local loopback or targeted 802.1Q VLAN interfaces at a controlled throughput of 10 Mbps for rule stress-testing.

```bash
# Replay via Local Loopback interface for basic inspection parsing verification (5,000 loops)
sudo tcpreplay -i lo -M 10 --loop=5000 /tmp/fixed_diameter_dos.pcap
sudo tcpreplay -i lo -M 10 --loop=5000 /tmp/fixed_ss7_attack.pcap

# Replay via dedicated VLAN interfaces to simulate partitioned Telecom Core infrastructure setups
sudo tcpreplay -i eno1.10 -M 10 --loop=5000 /tmp/fixed_diameter_dos.pcap
sudo tcpreplay -i eno1.20 -M 10 --loop=5000 /tmp/fixed_diameter_dos.pcap
sudo tcpreplay -i eno1.30 -M 10 --loop=5000 /tmp/fixed_diameter_dos.pcap
```
