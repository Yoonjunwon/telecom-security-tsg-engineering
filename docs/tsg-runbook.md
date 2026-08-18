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
