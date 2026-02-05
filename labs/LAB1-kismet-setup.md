# Lab 1: Kismet Setup & Configuration

**Duration:** 20 minutes  
**Objective:** Install and configure Kismet for wireless monitoring

---

## Overview

Kismet is an open-source wireless network detector, sniffer, and intrusion detection system. In this lab, you'll set up Kismet to monitor wireless traffic and detect attacks.

---

## Step 1: Install Kismet

### Kali Linux (pre-installed)
```bash
# Update to latest version
sudo apt update
sudo apt install -y kismet
```

### Ubuntu/Debian
```bash
# Add Kismet repository
wget -O - https://www.kismetwireless.net/repos/kismet-release.gpg.key | sudo apt-key add -
echo "deb https://www.kismetwireless.net/repos/apt/release/$(lsb_release -cs) $(lsb_release -cs) main" | sudo tee /etc/apt/sources.list.d/kismet.list

sudo apt update
sudo apt install -y kismet
```

### Add user to kismet group
```bash
sudo usermod -aG kismet $USER
# Log out and back in for group changes to take effect
```

---

## Step 2: Identify Your WiFi Adapter

```bash
# List wireless interfaces
iwconfig

# Or use ip command
ip link show

# Check monitor mode support
iw list | grep -A 10 "Supported interface modes"
```

**Expected output:** You should see your adapter (e.g., `wlan0`, `wlan1`)

---

## Step 3: Enable Monitor Mode (Manual)

```bash
# Bring interface down
sudo ip link set wlan1 down

# Set monitor mode
sudo iw wlan1 set monitor control

# Bring interface up
sudo ip link set wlan1 up

# Verify
iwconfig wlan1
# Should show: Mode:Monitor
```

**Note:** Kismet can also manage this automatically.

---

## Step 4: Start Kismet

### Basic Start
```bash
# Start Kismet (it will auto-configure sources)
kismet
```

### With Specific Interface
```bash
kismet -c wlan1
```

### Headless Mode (no web UI)
```bash
kismet -c wlan1 --no-ncurses
```

---

## Step 5: Access Web Interface

1. Open browser: `http://localhost:2501`
2. Default credentials (first run): Create admin user
3. Navigate through:
   - **Devices** - Discovered access points and clients
   - **Alerts** - Security alerts
   - **Datasources** - Capture interfaces

---

## Step 6: Explore the Interface

### Key Sections:

| Section | Purpose |
|---------|---------|
| **Devices** | All discovered WiFi devices (APs + Clients) |
| **SSIDs** | Network names detected |
| **Alerts** | Security alerts and warnings |
| **Datasources** | Capture interface status |
| **Packets** | Live packet statistics |
| **GPS** | Location data (if GPS connected) |

### Device Information Includes:
- MAC address & manufacturer
- Signal strength (RSSI)
- Encryption type (WPA2, WPA3, Open)
- Channel
- First/last seen timestamps
- Client associations

---

## Step 7: Verify Capture

1. Check that packets are being captured:
   - Status bar should show packet counts increasing
   - Devices should appear in device list

2. Verify your target AP ("CorpWiFi") is visible:
   - Search for SSID in the filter box
   - Note the BSSID (MAC address)

---

## Troubleshooting

### "No datasources available"
```bash
# Check if interface is in monitor mode
iwconfig wlan1

# Kill interfering processes
sudo airmon-ng check kill

# Restart Kismet
kismet -c wlan1
```

### "Permission denied"
```bash
# Ensure you're in kismet group
groups $USER

# Or run with sudo (not recommended for production)
sudo kismet -c wlan1
```

### Adapter not entering monitor mode
```bash
# Some adapters need NetworkManager disabled
sudo systemctl stop NetworkManager
sudo ip link set wlan1 down
sudo iw wlan1 set monitor control
sudo ip link set wlan1 up
```

---

## Checkpoint ✅

Before proceeding, verify:

- [ ] Kismet is running without errors
- [ ] Web interface is accessible at `http://localhost:2501`
- [ ] Your WiFi adapter is capturing packets
- [ ] Lab AP ("CorpWiFi") is visible in device list
- [ ] Alerts section is accessible (may be empty)

---

## Next Lab
→ [LAB2: Nzyme Setup](LAB2-nzyme-setup.md)
