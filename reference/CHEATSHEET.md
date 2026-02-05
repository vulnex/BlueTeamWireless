# Blue Team Wireless - Quick Reference

## Monitor Mode Setup

```bash
# Kill interfering processes
sudo airmon-ng check kill

# Manual monitor mode
sudo ip link set wlan1 down
sudo iw wlan1 set monitor control
sudo ip link set wlan1 up

# Verify
iwconfig wlan1  # Should show Mode:Monitor
```

---

## Kismet Commands

```bash
# Start with interface
kismet -c wlan1

# Headless mode
kismet -c wlan1 --no-ncurses

# Multiple interfaces
kismet -c wlan1 -c wlan2

# With logging
kismet -c wlan1 --log-types pcapng,kismet

# Web UI
http://localhost:2501
```

### Key Config Files
```
/etc/kismet/kismet.conf        # Main config (don't edit)
/etc/kismet/kismet_site.conf   # Your customizations
/etc/kismet/kismet_alerts.conf # Alert configuration
```

### Useful API Endpoints
```bash
# All devices
curl http://localhost:2501/devices/all_devices.json

# Alerts
curl http://localhost:2501/alerts/all_alerts.json

# Specific device
curl http://localhost:2501/devices/by-key/[DEVICE_KEY]/device.json
```

---

## Nzyme Commands

```bash
# Start manually
java -jar nzyme.jar -c /etc/nzyme/nzyme.conf

# Service management
sudo systemctl start nzyme
sudo systemctl stop nzyme
sudo systemctl status nzyme
sudo journalctl -u nzyme -f

# Web UI
http://localhost:22900
```

### Key Config Files
```
/etc/nzyme/nzyme.conf          # Main configuration
```

---

## Key Kismet Alerts

| Alert | Meaning |
|-------|---------|
| DEAUTHFLOOD | Deauth DoS attack |
| BCASTDISCON | Broadcast disconnect |
| APSPOOF | Evil twin / Rogue AP |
| ADVCRYPTCHANGE | Encryption changed |
| CHANCHANGE | Channel changed |
| CRYPTODROP | Security downgraded |
| KARMAOUI | Karma attack (00:13:37 OUI) |
| WPSBRUTE | WPS brute force |
| DEVICEFOUND | New device detected |

---

## Nzyme Alert Types

| Alert | Meaning |
|-------|---------|
| DEAUTH_FLOOD | Deauth attack |
| UNEXPECTED_BSSID | Evil twin |
| UNEXPECTED_CHANNEL | Wrong channel |
| UNEXPECTED_SECURITY | Encryption mismatch |
| UNKNOWN_SSID | Rogue AP |
| BANDIT_CONTACT | Known attack device |

---

## Attack Detection Matrix

| Attack | Kismet Alert | Nzyme Alert |
|--------|--------------|-------------|
| Deauth DoS | DEAUTHFLOOD | DEAUTH_FLOOD |
| Evil Twin | APSPOOF | UNEXPECTED_BSSID |
| Karma | KARMAOUI | UNEXPECTED_BSSID |
| Rogue AP | DEVICEFOUND | UNKNOWN_SSID |
| Encryption Downgrade | CRYPTODROP | UNEXPECTED_SECURITY |
| WPS Brute Force | WPSBRUTE | - |
| Channel Hijack | CHANCHANGE | UNEXPECTED_CHANNEL |

---

## Signal Strength Reference

| RSSI (dBm) | Quality | Distance |
|------------|---------|----------|
| -30 to -50 | Excellent | Very close |
| -50 to -60 | Good | Same room |
| -60 to -70 | Fair | Adjacent room |
| -70 to -80 | Weak | Different floor |
| -80 to -90 | Poor | Far away |
| < -90 | Unusable | Edge of range |

---

## OUI Lookup

```bash
# Quick lookup
grep "00:13:37" /usr/share/ieee-data/oui.txt

# Common suspicious OUIs
00:13:37 - WiFi Pineapple / Karma
00:0C:F1 - Intel (common in laptops)
DA:A1:19 - Randomized MAC indicator
```

### Online Tools
- https://macvendors.com
- https://maclookup.app

---

## Response Checklist

### Deauth Attack
- [ ] Document source MAC
- [ ] Note target AP/clients
- [ ] Estimate attacker location (RSSI)
- [ ] Check for follow-up attacks (evil twin)
- [ ] Enable MFP if possible

### Evil Twin
- [ ] Document rogue BSSID
- [ ] Compare with legitimate AP
- [ ] Check for connected clients
- [ ] Physical location hunt
- [ ] Block MAC if possible

### Rogue AP
- [ ] Verify it's not authorized
- [ ] Document all details
- [ ] Locate physically
- [ ] Remove/disable
- [ ] Investigate source

---

## Useful Commands

```bash
# List wireless interfaces
iw dev

# Scan for networks
sudo iw wlan0 scan | grep -E "SSID|BSS|signal"

# Monitor channel
sudo iw wlan1 set channel 6

# Check interface mode
iwconfig wlan1

# Kill NetworkManager
sudo systemctl stop NetworkManager

# Packet capture with tcpdump
sudo tcpdump -i wlan1 -w capture.pcap

# Filter 802.11 frames (Wireshark)
wlan.fc.type_subtype == 0x0c  # Deauth
wlan.fc.type_subtype == 0x00  # Association request
```

---

## Config Templates

### kismet_site.conf (Detection Focus)
```conf
# Enable key alerts
alert=DEAUTHFLOOD,10/min,5/sec
alert=BCASTDISCON,10/min,5/sec
alert=APSPOOF,5/min,1/sec
alert=ADVCRYPTCHANGE,5/min,1/sec
alert=CRYPTODROP,5/min,1/sec
alert=KARMAOUI,5/min,1/sec

# Define legitimate APs
apspoof=CorpWiFi:ssid="CorpWiFi",validmacs="AA:BB:CC:DD:EE:FF"
```

### Nzyme Network Definition
```hocon
dot11.networks = [{
  ssid = "CorpWiFi"
  bssids = ["AA:BB:CC:DD:EE:FF"]
  channels = [6]
  security = ["WPA2-PSK"]
}]
```
