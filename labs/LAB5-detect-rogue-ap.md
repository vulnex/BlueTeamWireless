# Lab 5: Detecting Rogue APs & Advanced Attacks

**Duration:** 20 minutes  
**Objective:** Detect unauthorized access points and advanced wireless attacks

---

## Attack Overview

### Rogue AP Types

| Type | Description | Risk |
|------|-------------|------|
| **Unauthorized AP** | Employee plugs in personal router | Network bypass |
| **Misconfigured AP** | IT error (wrong VLAN, open auth) | Data exposure |
| **Malicious AP** | Attacker-deployed device | Full compromise |
| **Soft AP** | Laptop sharing connection | Pivot point |

### Advanced Attacks Covered
- Probe request tracking
- PMKID capture attempts
- WPS brute force
- Beacon flood (DoS)

---

## Detecting Rogue APs

### Strategy
1. **Inventory known APs** - All legitimate BSSIDs
2. **Monitor for new APs** - Alert on unknown devices
3. **Check connected network** - Is AP on your wired network?
4. **Physical location** - Use signal strength to locate

---

## Kismet: Rogue AP Detection

### Track New Devices
```conf
# kismet_site.conf

# Alert when new AP is seen (after learning period)
alert=DEVICEFOUND,10/min,5/sec

# List of allowed AP MACs (everything else is rogue)
devicefound=AA:BB:CC:DD:EE:FF
devicefound=AA:BB:CC:DD:EE:00/FF:FF:FF:FF:FF:00  # MAC mask

# Alert when device disappears (intermittent rogue)
alert=DEVICELOST,10/min,5/sec
```

### Manual Inspection

1. Go to **Devices** → Filter by **Type: AP**
2. Sort by **First Seen** (newest first)
3. Check each AP against inventory:
   - Known MAC? 
   - Expected SSID?
   - Appropriate channel?

### Kismet CLI Query
```bash
# List all APs seen
kismet_client -e "DEVICES" | grep "Wi-Fi AP"

# Export to JSON for analysis
curl http://localhost:2501/devices/all_devices.json
```

---

## Nzyme: Rogue AP Detection

### Configure Known Networks
```hocon
# All APs NOT in this list = potential rogues
dot11.networks = [
  {
    ssid = "CorpWiFi"
    bssids = ["AA:BB:CC:DD:EE:FF", "AA:BB:CC:DD:EE:00"]
    channels = [1, 6, 11]
    security = ["WPA2-PSK", "WPA3-PSK"]
  },
  {
    ssid = "CorpGuest"
    bssids = ["AA:BB:CC:DD:EE:01"]
    channels = [6]
    security = ["WPA2-PSK"]
  }
]

dot11.traps {
  # Alert on any AP not in whitelist
  unknown_ssid {
    enabled = true
  }
}
```

### Dashboard Analysis
1. **Networks** → View all detected SSIDs
2. **Unmonitored Networks** shows potential rogues
3. Check OUI/vendor for each unknown AP

---

## Detecting Probe Request Tracking

### The Threat
Attackers capture probe requests to:
- Track device movement
- Identify user patterns
- Harvest SSIDs client is looking for
- Target specific users

### Detection in Kismet
```conf
# Monitor for excessive probing (tracking station)
alert=PROBEREQFLOOD,10/min,5/sec
```

### What to Look For
- Device sending many probe requests
- Device not connecting to anything (just listening)
- Device with suspicious vendor (or randomized MAC)

---

## Detecting WPS Attacks

### WPS Brute Force (Reaver/Bully)
Repeated WPS authentication attempts indicate brute force attack.

### Kismet Alert
```conf
# WPS brute force detection
alert=WPSBRUTE,5/min,1/sec
```

### Signs of Attack
- Many WPS exchanges from same client
- Failed WPS authentications
- Single client, repeated attempts

---

## Detecting Beacon Floods

### The Attack
Flooding the spectrum with fake beacons:
- Confuses clients
- Hides legitimate networks
- Denial of service

### Kismet Detection
- Sudden spike in "new" APs
- Many APs with random/generated SSIDs
- All from similar signal location

### Manual Detection
```bash
# Watch beacon count in real-time
watch -n 1 'iwconfig wlan1 | grep -i beacon'
```

---

## Hands-On Exercise

### Scenario
The instructor will demonstrate multiple attack types:
1. Rogue AP deployment
2. Probe capture station
3. Optional: Beacon flood

### Your Task

1. **Identify the Rogue AP:**

| Field | Value |
|-------|-------|
| SSID | |
| BSSID | |
| Channel | |
| OUI/Vendor | |
| First Seen | |

2. **Identify Probe Tracker:**

| Field | Value |
|-------|-------|
| Device MAC | |
| Probe Count | |
| Time Window | |
| Suspicious? | [ ] Yes [ ] No |

3. **Categorize threat level:**

| Device | Type | Severity |
|--------|------|----------|
| | Rogue AP | |
| | Tracker | |
| | Evil Twin | |

---

## Signal-Based Location

### Triangulation Technique
1. Note signal strength (RSSI) from multiple positions
2. Stronger signal = closer to device
3. Walk toward strongest signal
4. Use directional antenna if available

### Kismet Signal History
- Click device → Signal tab
- Watch for RSSI changes as you move
- -30 dBm = very close
- -70 dBm = moderate distance
- -90 dBm = far/weak

### Tools for Location
- **Kismet** - Signal history graph
- **WiFi Analyzer** - Android app
- **Ekahau** - Professional WLAN survey

---

## Response Playbook

### Rogue AP Detected
```
1. ALERT security team
2. DOCUMENT device details (MAC, SSID, location estimate)
3. LOCATE device using signal triangulation
4. DETERMINE if hostile or misconfigured
5. REMOVE or ISOLATE the device
6. INVESTIGATE how it got there
7. UPDATE detection rules
```

### Evidence Collection
```bash
# Capture packets for forensics
kismet -c wlan1 --log-types pcapng

# Export Kismet database
cp ~/.kismet/kismet*.kismet /evidence/

# Screenshot alerts
# Document timeline
```

---

## Compliance Mapping

| Requirement | Detection |
|-------------|-----------|
| **PCI-DSS 11.1** | Quarterly wireless scan → Kismet/Nzyme inventory |
| **PCI-DSS 11.1.2** | Detect unauthorized wireless | 
| **NIST 800-153** | Continuous wireless monitoring |
| **CIS Control 12** | Network infrastructure management |

---

## Expected Results

### Rogue AP Alert (Nzyme)
```
Alert: UNKNOWN_SSID
SSID: "FreeWiFi"
BSSID: XX:XX:XX:XX:XX:XX
Channel: 11
Security: OPEN
Severity: HIGH
First Seen: 14:32:15
```

### Tracking Station (Kismet)
```
Device: XX:XX:XX:XX:XX:XX
Type: Wi-Fi Client
Probes Sent: 247
Networks Probed: 15
Associated: No (passive collection)
Alert: Possible tracking station
```

---

## Checkpoint ✅

- [ ] Identified rogue AP in monitoring tools
- [ ] Detected probe capture activity
- [ ] Understand signal-based location technique
- [ ] Know response playbook steps
- [ ] Understand compliance requirements

---

## Workshop Complete! 🎉

### Summary
You've learned to:
1. ✅ Set up Kismet for wireless IDS
2. ✅ Deploy Nzyme for WiFi monitoring
3. ✅ Detect deauthentication attacks
4. ✅ Identify evil twin/rogue APs
5. ✅ Monitor for tracking stations
6. ✅ Respond to wireless incidents

### Next Steps
- Deploy in your environment
- Customize alerts for your networks
- Integrate with SIEM
- Regular wireless assessments
- User awareness training

### Resources
- Kismet: https://www.kismetwireless.net
- Nzyme: https://www.nzyme.org
- 802.11 Security: IEEE standards
- WLAN Security Best Practices: NIST SP 800-153
