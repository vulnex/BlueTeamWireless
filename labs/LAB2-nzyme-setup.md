# Lab 2: Nzyme Setup & Configuration

**Duration:** 25 minutes  
**Objective:** Deploy Nzyme for WiFi intrusion detection and alerting

---

## Overview

Nzyme is a WiFi defense system that provides:
- Real-time wireless monitoring
- Evil twin / rogue AP detection
- Deauthentication attack detection
- Alerting and dashboards
- Compliance reporting

---

## Step 1: System Requirements

### Minimum Hardware
- 2 CPU cores
- 4GB RAM
- 20GB disk space
- USB WiFi adapter (monitor mode capable)

### Software Prerequisites
```bash
# Install Java 17+
sudo apt install -y openjdk-17-jdk

# Verify Java version
java -version

# Install additional dependencies
sudo apt install -y libpcap-dev wireless-tools
```

---

## Step 2: Download Nzyme

```bash
# Create installation directory
sudo mkdir -p /opt/nzyme
cd /opt/nzyme

# Download latest release (check nzyme.org for current version)
sudo wget https://github.com/nzymedefense/nzyme/releases/download/2.0.0-alpha.X/nzyme-2.0.0-alpha.X.jar -O nzyme.jar

# Or clone from source
git clone https://github.com/nzymedefense/nzyme.git
cd nzyme
mvn package -DskipTests
```

---

## Step 3: Create Configuration

```bash
# Copy example config
sudo cp nzyme.conf.example /etc/nzyme/nzyme.conf

# Edit configuration
sudo nano /etc/nzyme/nzyme.conf
```

### Minimal Configuration
```hocon
# /etc/nzyme/nzyme.conf

general {
  role = "LEADER"
  admin_password_hash = "your_bcrypt_hash_here"
}

interfaces {
  rest_listen_uri = "http://0.0.0.0:22900/"
  http_external_uri = "http://localhost:22900/"
}

# 802.11/WiFi Configuration
dot11 {
  networks = [
    {
      ssid = "CorpWiFi"
      channels = [1, 6, 11]
      security = ["WPA2-PSK"]
      bssids = ["AA:BB:CC:DD:EE:FF"]  # Replace with your AP's MAC
    }
  ]
  
  # Monitored BSSIDs (legitimate APs)
  traps {
    # Alert on these attacks
    deauth_monitor {
      enabled = true
      threshold = 10  # alerts after 10 deauth frames in window
      window_seconds = 60
    }
    
    # Unexpected BSSID for known SSID = Evil Twin
    unexpected_bssid {
      enabled = true
    }
    
    # Unexpected channel for known network
    unexpected_channel {
      enabled = true
    }
  }
}

# Capture interfaces
802_11_monitors = [
  {
    device = "wlan1"
    channels = [1, 6, 11]
    channel_hop_interval = "1s"
  }
]

# Database
postgresql {
  host = "localhost"
  port = 5432
  database = "nzyme"
  username = "nzyme"
  password = "nzyme_password"
}
```

---

## Step 4: Set Up Database (PostgreSQL)

```bash
# Install PostgreSQL
sudo apt install -y postgresql postgresql-contrib

# Start PostgreSQL
sudo systemctl start postgresql
sudo systemctl enable postgresql

# Create database and user
sudo -u postgres psql <<EOF
CREATE USER nzyme WITH PASSWORD 'nzyme_password';
CREATE DATABASE nzyme OWNER nzyme;
GRANT ALL PRIVILEGES ON DATABASE nzyme TO nzyme;
EOF
```

---

## Step 5: Generate Admin Password Hash

```bash
# Generate bcrypt hash for your admin password
# Option 1: Use online bcrypt generator
# Option 2: Use htpasswd
htpasswd -bnBC 12 "" "YourSecurePassword" | tr -d ':\n'

# Option 3: Python
python3 -c "import bcrypt; print(bcrypt.hashpw(b'YourSecurePassword', bcrypt.gensalt(12)).decode())"
```

Add the hash to your config file.

---

## Step 6: Start Nzyme

### Manual Start
```bash
cd /opt/nzyme
sudo java -jar nzyme.jar -c /etc/nzyme/nzyme.conf
```

### As a Service
```bash
# Create systemd service
sudo tee /etc/systemd/system/nzyme.service > /dev/null <<EOF
[Unit]
Description=Nzyme WiFi Defense System
After=network.target postgresql.service

[Service]
Type=simple
User=root
WorkingDirectory=/opt/nzyme
ExecStart=/usr/bin/java -jar /opt/nzyme/nzyme.jar -c /etc/nzyme/nzyme.conf
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
EOF

# Enable and start
sudo systemctl daemon-reload
sudo systemctl enable nzyme
sudo systemctl start nzyme

# Check status
sudo systemctl status nzyme
```

---

## Step 7: Access Web Interface

1. Open browser: `http://localhost:22900`
2. Login with admin credentials
3. Navigate the dashboard:
   - **Overview** - System status
   - **Networks** - Monitored SSIDs
   - **Alerts** - Detection events
   - **Taps** - Capture interfaces

---

## Step 8: Configure Detection Rules

### Key Detection Capabilities

| Detection | Description |
|-----------|-------------|
| **Deauth/Disassoc Monitor** | Detects flood of deauth frames |
| **Unexpected BSSID** | Evil twin using same SSID, different MAC |
| **Unexpected Channel** | Known AP appearing on wrong channel |
| **Unexpected Fingerprint** | AP with changed characteristics |
| **Bandit Contacts** | Known attack device MACs (Pineapple, etc.) |
| **Probe Request Monitor** | Excessive probing behavior |

### Define Monitored Networks

In the web UI:
1. Go to **Networks** → **Add Network**
2. Enter:
   - SSID: `CorpWiFi`
   - Expected BSSIDs (MAC addresses)
   - Expected channels
   - Expected security (WPA2, WPA3)

---

## Step 9: Test Detection

### Verify Interface Capture
```bash
# Check logs
sudo journalctl -u nzyme -f

# Look for:
# - "Starting 802.11 monitor on wlan1"
# - "Captured X frames"
```

### Trigger Test Alert
The instructor will demonstrate a deauth attack. You should see:
- Alert in Nzyme dashboard
- Details: Source MAC, target, frame count

---

## Troubleshooting

### "Cannot start monitor on interface"
```bash
# Interface might be in use
sudo airmon-ng check kill
sudo systemctl stop NetworkManager
```

### "Database connection failed"
```bash
# Check PostgreSQL is running
sudo systemctl status postgresql

# Test connection
psql -h localhost -U nzyme -d nzyme
```

### No packets captured
```bash
# Verify monitor mode
iwconfig wlan1

# Check interface is not down
sudo ip link set wlan1 up
```

---

## Checkpoint ✅

Before proceeding, verify:

- [ ] Nzyme is running (`systemctl status nzyme`)
- [ ] Web interface accessible at `http://localhost:22900`
- [ ] WiFi adapter is capturing frames (check logs)
- [ ] Lab network "CorpWiFi" is configured as monitored network
- [ ] Detection rules are enabled

---

## Next Lab
→ [LAB3: Detecting Deauthentication Attacks](LAB3-detect-deauth.md)
