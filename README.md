# darkstat Installation Guide

darkstat is a free and open-source network statistics. darkstat captures network traffic and provides statistics

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
  - RAM: 128MB minimum
  - Storage: 1GB for data
  - Network: HTTP interface
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 667 (default darkstat port)
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

# Install darkstat
sudo dnf install -y darkstat

# Enable and start service
sudo systemctl enable --now darkstat

# Configure firewall
sudo firewall-cmd --permanent --add-port=667/tcp
sudo firewall-cmd --reload

# Verify installation
darkstat --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install darkstat
sudo apt install -y darkstat

# Enable and start service
sudo systemctl enable --now darkstat

# Configure firewall
sudo ufw allow 667

# Verify installation
darkstat --version
```

### Arch Linux

```bash
# Install darkstat
sudo pacman -S darkstat

# Enable and start service
sudo systemctl enable --now darkstat

# Verify installation
darkstat --version
```

### Alpine Linux

```bash
# Install darkstat
apk add --no-cache darkstat

# Enable and start service
rc-update add darkstat default
rc-service darkstat start

# Verify installation
darkstat --version
```

### openSUSE/SLES

```bash
# Install darkstat
sudo zypper install -y darkstat

# Enable and start service
sudo systemctl enable --now darkstat

# Configure firewall
sudo firewall-cmd --permanent --add-port=667/tcp
sudo firewall-cmd --reload

# Verify installation
darkstat --version
```

### macOS

```bash
# Using Homebrew
brew install darkstat

# Start service
brew services start darkstat

# Verify installation
darkstat --version
```

### FreeBSD

```bash
# Using pkg
pkg install darkstat

# Enable in rc.conf
echo 'darkstat_enable="YES"' >> /etc/rc.conf

# Start service
service darkstat start

# Verify installation
darkstat --version
```

### Windows

```bash
# Using Chocolatey
choco install darkstat

# Or using Scoop
scoop install darkstat

# Verify installation
darkstat --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/darkstat

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
darkstat --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable darkstat

# Start service
sudo systemctl start darkstat

# Stop service
sudo systemctl stop darkstat

# Restart service
sudo systemctl restart darkstat

# Check status
sudo systemctl status darkstat

# View logs
sudo journalctl -u darkstat -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add darkstat default

# Start service
rc-service darkstat start

# Stop service
rc-service darkstat stop

# Restart service
rc-service darkstat restart

# Check status
rc-service darkstat status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'darkstat_enable="YES"' >> /etc/rc.conf

# Start service
service darkstat start

# Stop service
service darkstat stop

# Restart service
service darkstat restart

# Check status
service darkstat status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start darkstat
brew services stop darkstat
brew services restart darkstat

# Check status
brew services list | grep darkstat
```

### Windows Service Manager

```powershell
# Start service
net start darkstat

# Stop service
net stop darkstat

# Using PowerShell
Start-Service darkstat
Stop-Service darkstat
Restart-Service darkstat

# Check status
Get-Service darkstat
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream darkstat_backend {
    server 127.0.0.1:667;
}

server {
    listen 80;
    server_name darkstat.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name darkstat.example.com;

    ssl_certificate /etc/ssl/certs/darkstat.example.com.crt;
    ssl_certificate_key /etc/ssl/private/darkstat.example.com.key;

    location / {
        proxy_pass http://darkstat_backend;
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
    ServerName darkstat.example.com
    Redirect permanent / https://darkstat.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName darkstat.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/darkstat.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/darkstat.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:667/
    ProxyPassReverse / http://127.0.0.1:667/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend darkstat_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/darkstat.pem
    redirect scheme https if !{ ssl_fc }
    default_backend darkstat_backend

backend darkstat_backend
    balance roundrobin
    server darkstat1 127.0.0.1:667 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R darkstat:darkstat /etc/darkstat
sudo chmod 750 /etc/darkstat

# Configure firewall
sudo firewall-cmd --permanent --add-port=667/tcp
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
sudo systemctl status darkstat

# View logs
sudo journalctl -u darkstat -f

# Monitor resource usage
top -p $(pgrep darkstat)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/darkstat"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/darkstat-backup-$DATE.tar.gz" /etc/darkstat /var/lib/darkstat

echo "Backup completed: $BACKUP_DIR/darkstat-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop darkstat

# Restore from backup
tar -xzf /backup/darkstat/darkstat-backup-*.tar.gz -C /

# Start service
sudo systemctl start darkstat
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u darkstat -n 100
sudo tail -f /var/log/darkstat/darkstat.log

# Check configuration
darkstat --version

# Check permissions
ls -la /etc/darkstat
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 667

# Test connectivity
telnet localhost 667

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep darkstat)

# Check disk I/O
iotop -p $(pgrep darkstat)

# Check connections
ss -an | grep 667
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  darkstat:
    image: darkstat:latest
    ports:
      - "667:667"
    volumes:
      - ./config:/etc/darkstat
      - ./data:/var/lib/darkstat
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update darkstat

# Debian/Ubuntu
sudo apt update && sudo apt upgrade darkstat

# Arch Linux
sudo pacman -Syu darkstat

# Alpine Linux
apk update && apk upgrade darkstat

# openSUSE
sudo zypper update darkstat

# FreeBSD
pkg update && pkg upgrade darkstat

# Always backup before updates
tar -czf /backup/darkstat-pre-update-$(date +%Y%m%d).tar.gz /etc/darkstat

# Restart after updates
sudo systemctl restart darkstat
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/darkstat

# Clean old logs
find /var/log/darkstat -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/darkstat
```

## Additional Resources

- Official Documentation: https://docs.darkstat.org/
- GitHub Repository: https://github.com/darkstat/darkstat
- Community Forum: https://forum.darkstat.org/
- Best Practices Guide: https://docs.darkstat.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
