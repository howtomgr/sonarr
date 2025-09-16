# sonarr Installation Guide

sonarr is a free and open-source TV series management. Sonarr automates TV show downloading and organization with calendar and quality management

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
  - Storage: 1GB for app
  - Network: HTTP/HTTPS access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8989 (default sonarr port)
  - None
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

# Install sonarr
sudo dnf install -y sonarr

# Enable and start service
sudo systemctl enable --now sonarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=8989/tcp
sudo firewall-cmd --reload

# Verify installation
sonarr --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install sonarr
sudo apt install -y sonarr

# Enable and start service
sudo systemctl enable --now sonarr

# Configure firewall
sudo ufw allow 8989

# Verify installation
sonarr --version
```

### Arch Linux

```bash
# Install sonarr
sudo pacman -S sonarr

# Enable and start service
sudo systemctl enable --now sonarr

# Verify installation
sonarr --version
```

### Alpine Linux

```bash
# Install sonarr
apk add --no-cache sonarr

# Enable and start service
rc-update add sonarr default
rc-service sonarr start

# Verify installation
sonarr --version
```

### openSUSE/SLES

```bash
# Install sonarr
sudo zypper install -y sonarr

# Enable and start service
sudo systemctl enable --now sonarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=8989/tcp
sudo firewall-cmd --reload

# Verify installation
sonarr --version
```

### macOS

```bash
# Using Homebrew
brew install sonarr

# Start service
brew services start sonarr

# Verify installation
sonarr --version
```

### FreeBSD

```bash
# Using pkg
pkg install sonarr

# Enable in rc.conf
echo 'sonarr_enable="YES"' >> /etc/rc.conf

# Start service
service sonarr start

# Verify installation
sonarr --version
```

### Windows

```bash
# Using Chocolatey
choco install sonarr

# Or using Scoop
scoop install sonarr

# Verify installation
sonarr --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/sonarr

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
sonarr --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable sonarr

# Start service
sudo systemctl start sonarr

# Stop service
sudo systemctl stop sonarr

# Restart service
sudo systemctl restart sonarr

# Check status
sudo systemctl status sonarr

# View logs
sudo journalctl -u sonarr -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add sonarr default

# Start service
rc-service sonarr start

# Stop service
rc-service sonarr stop

# Restart service
rc-service sonarr restart

# Check status
rc-service sonarr status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'sonarr_enable="YES"' >> /etc/rc.conf

# Start service
service sonarr start

# Stop service
service sonarr stop

# Restart service
service sonarr restart

# Check status
service sonarr status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start sonarr
brew services stop sonarr
brew services restart sonarr

# Check status
brew services list | grep sonarr
```

### Windows Service Manager

```powershell
# Start service
net start sonarr

# Stop service
net stop sonarr

# Using PowerShell
Start-Service sonarr
Stop-Service sonarr
Restart-Service sonarr

# Check status
Get-Service sonarr
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream sonarr_backend {
    server 127.0.0.1:8989;
}

server {
    listen 80;
    server_name sonarr.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name sonarr.example.com;

    ssl_certificate /etc/ssl/certs/sonarr.example.com.crt;
    ssl_certificate_key /etc/ssl/private/sonarr.example.com.key;

    location / {
        proxy_pass http://sonarr_backend;
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
    ServerName sonarr.example.com
    Redirect permanent / https://sonarr.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName sonarr.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/sonarr.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/sonarr.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8989/
    ProxyPassReverse / http://127.0.0.1:8989/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend sonarr_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/sonarr.pem
    redirect scheme https if !{ ssl_fc }
    default_backend sonarr_backend

backend sonarr_backend
    balance roundrobin
    server sonarr1 127.0.0.1:8989 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R sonarr:sonarr /etc/sonarr
sudo chmod 750 /etc/sonarr

# Configure firewall
sudo firewall-cmd --permanent --add-port=8989/tcp
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
sudo systemctl status sonarr

# View logs
sudo journalctl -u sonarr -f

# Monitor resource usage
top -p $(pgrep sonarr)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/sonarr"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/sonarr-backup-$DATE.tar.gz" /etc/sonarr /var/lib/sonarr

echo "Backup completed: $BACKUP_DIR/sonarr-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop sonarr

# Restore from backup
tar -xzf /backup/sonarr/sonarr-backup-*.tar.gz -C /

# Start service
sudo systemctl start sonarr
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u sonarr -n 100
sudo tail -f /var/log/sonarr/sonarr.log

# Check configuration
sonarr --version

# Check permissions
ls -la /etc/sonarr
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8989

# Test connectivity
telnet localhost 8989

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep sonarr)

# Check disk I/O
iotop -p $(pgrep sonarr)

# Check connections
ss -an | grep 8989
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  sonarr:
    image: sonarr:latest
    ports:
      - "8989:8989"
    volumes:
      - ./config:/etc/sonarr
      - ./data:/var/lib/sonarr
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update sonarr

# Debian/Ubuntu
sudo apt update && sudo apt upgrade sonarr

# Arch Linux
sudo pacman -Syu sonarr

# Alpine Linux
apk update && apk upgrade sonarr

# openSUSE
sudo zypper update sonarr

# FreeBSD
pkg update && pkg upgrade sonarr

# Always backup before updates
tar -czf /backup/sonarr-pre-update-$(date +%Y%m%d).tar.gz /etc/sonarr

# Restart after updates
sudo systemctl restart sonarr
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/sonarr

# Clean old logs
find /var/log/sonarr -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/sonarr
```

## Additional Resources

- Official Documentation: https://docs.sonarr.org/
- GitHub Repository: https://github.com/sonarr/sonarr
- Community Forum: https://forum.sonarr.org/
- Best Practices Guide: https://docs.sonarr.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
