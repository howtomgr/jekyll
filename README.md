# jekyll Installation Guide

jekyll is a free and open-source static site generator. Jekyll transforms plain text into static websites and blogs

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 1 core minimum
  - RAM: 512MB minimum
  - Storage: 1GB for sites
  - Network: Build tool
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port N/A (default jekyll port)
  - Dev server on 4000
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install jekyll
sudo dnf install -y jekyll

# Enable and start service
sudo systemctl enable --now jekyll

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
jekyll --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install jekyll
sudo apt install -y jekyll

# Enable and start service
sudo systemctl enable --now jekyll

# Configure firewall
sudo ufw allow N/A

# Verify installation
jekyll --version
```

### Arch Linux

```bash
# Install jekyll
sudo pacman -S jekyll

# Enable and start service
sudo systemctl enable --now jekyll

# Verify installation
jekyll --version
```

### Alpine Linux

```bash
# Install jekyll
apk add --no-cache jekyll

# Enable and start service
rc-update add jekyll default
rc-service jekyll start

# Verify installation
jekyll --version
```

### openSUSE/SLES

```bash
# Install jekyll
sudo zypper install -y jekyll

# Enable and start service
sudo systemctl enable --now jekyll

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Verify installation
jekyll --version
```

### macOS

```bash
# Using Homebrew
brew install jekyll

# Start service
brew services start jekyll

# Verify installation
jekyll --version
```

### FreeBSD

```bash
# Using pkg
pkg install jekyll

# Enable in rc.conf
echo 'jekyll_enable="YES"' >> /etc/rc.conf

# Start service
service jekyll start

# Verify installation
jekyll --version
```

### Windows

```bash
# Using Chocolatey
choco install jekyll

# Or using Scoop
scoop install jekyll

# Verify installation
jekyll --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/jekyll

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
jekyll --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable jekyll

# Start service
sudo systemctl start jekyll

# Stop service
sudo systemctl stop jekyll

# Restart service
sudo systemctl restart jekyll

# Check status
sudo systemctl status jekyll

# View logs
sudo journalctl -u jekyll -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add jekyll default

# Start service
rc-service jekyll start

# Stop service
rc-service jekyll stop

# Restart service
rc-service jekyll restart

# Check status
rc-service jekyll status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'jekyll_enable="YES"' >> /etc/rc.conf

# Start service
service jekyll start

# Stop service
service jekyll stop

# Restart service
service jekyll restart

# Check status
service jekyll status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start jekyll
brew services stop jekyll
brew services restart jekyll

# Check status
brew services list | grep jekyll
```

### Windows Service Manager

```powershell
# Start service
net start jekyll

# Stop service
net stop jekyll

# Using PowerShell
Start-Service jekyll
Stop-Service jekyll
Restart-Service jekyll

# Check status
Get-Service jekyll
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream jekyll_backend {
    server 127.0.0.1:N/A;
}

server {
    listen 80;
    server_name jekyll.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name jekyll.example.com;

    ssl_certificate /etc/ssl/certs/jekyll.example.com.crt;
    ssl_certificate_key /etc/ssl/private/jekyll.example.com.key;

    location / {
        proxy_pass http://jekyll_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName jekyll.example.com
    Redirect permanent / https://jekyll.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName jekyll.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/jekyll.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/jekyll.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:N/A/
    ProxyPassReverse / http://127.0.0.1:N/A/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend jekyll_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/jekyll.pem
    redirect scheme https if !{ ssl_fc }
    default_backend jekyll_backend

backend jekyll_backend
    balance roundrobin
    server jekyll1 127.0.0.1:N/A check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R jekyll:jekyll /etc/jekyll
sudo chmod 750 /etc/jekyll

# Configure firewall
sudo firewall-cmd --permanent --add-port=N/A/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status jekyll

# View logs
sudo journalctl -u jekyll -f

# Monitor resource usage
top -p $(pgrep jekyll)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/jekyll"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/jekyll-backup-$DATE.tar.gz" /etc/jekyll /var/lib/jekyll

echo "Backup completed: $BACKUP_DIR/jekyll-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop jekyll

# Restore from backup
tar -xzf /backup/jekyll/jekyll-backup-*.tar.gz -C /

# Start service
sudo systemctl start jekyll
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u jekyll -n 100
sudo tail -f /var/log/jekyll/jekyll.log

# Check configuration
jekyll --version

# Check permissions
ls -la /etc/jekyll
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep N/A

# Test connectivity
telnet localhost N/A

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep jekyll)

# Check disk I/O
iotop -p $(pgrep jekyll)

# Check connections
ss -an | grep N/A
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  jekyll:
    image: jekyll:latest
    ports:
      - "N/A:N/A"
    volumes:
      - ./config:/etc/jekyll
      - ./data:/var/lib/jekyll
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update jekyll

# Debian/Ubuntu
sudo apt update && sudo apt upgrade jekyll

# Arch Linux
sudo pacman -Syu jekyll

# Alpine Linux
apk update && apk upgrade jekyll

# openSUSE
sudo zypper update jekyll

# FreeBSD
pkg update && pkg upgrade jekyll

# Always backup before updates
tar -czf /backup/jekyll-pre-update-$(date +%Y%m%d).tar.gz /etc/jekyll

# Restart after updates
sudo systemctl restart jekyll
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/jekyll

# Clean old logs
find /var/log/jekyll -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/jekyll
```

## Additional Resources

- Official Documentation: https://docs.jekyll.org/
- GitHub Repository: https://github.com/jekyll/jekyll
- Community Forum: https://forum.jekyll.org/
- Best Practices Guide: https://docs.jekyll.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
