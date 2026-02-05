# Lab 4: Detecting Evil Twin Attacks

**Duration:** 20 minutes  
**Objective:** Detect rogue access points impersonating legitimate networks

---

## Attack Overview

Evil Twin attacks create a fake AP that:
- Uses the **same SSID** as a legitimate network
- May use a **different BSSID** (MAC address)
- May operate on a **different channel**
- Tricks clients into connecting
- Enables credential theft via captive portals

### Attack Variants

| Attack | Description |
|--------|-------------|
| **Basic Evil Twin** | Same SSID, different MAC |
| **Karma** | Responds to ANY probe request |
| **MANA** | Enhanced Karma with more features |
| **WiFi Pineapple** | Hardware-based attack platform |

### Attack Tools (Instructor Demo)
- WiFi Pineapple (Evil Twin + Karma)
- `hostapd-mana`
- `airbase-ng`
- `fluxion`

---

## Detection Strategy

### What to Look For

| Indicator | Suspicious If... |
|-----------|------------------|
| **BSSID** | New MAC for known SSID |
| **Channel** | Network on unexpected channel |
| **Signal** | Unusual signal strength |
| **Security** | Encryption downgrade (WPA2→Open) |
| **Beacon Rate** | Different from legitimate AP |
| **Vendor OUI** | Different manufacturer |

---

## Detection with Kismet

### Relevant Kismet Alerts

| Alert | Description |
|-------|-------------|
| `APSPOOF` | SSID from unexpected MAC address |
| `ADVCRYPTCHANGE` | Encryption changed for known SSID |
| `CHANCHANGE` | AP moved to different channel |
| `BEACONRATE` | Beacon interval changed |
| `BSSTIMESTAMP` | Invalid/suspicious BSS timestamp |
| `CRYPTODROP` | Encryption downgraded |

### Configure APSPOOF Detection

In `kismet_site.conf`:
```conf
# Define legitimate APs for "CorpWiFi"
apspoof=CorpWiFi:ssid="CorpWiFi",validmacs="AA:BB:CC:DD:EE:FF"

# Can use MAC mask for multiple APs from same vendor
apspoof=CorpWiFi:ssid="CorpWiFi",validmacs="AA:BB:CC:00:00:00/FF:FF:FF:00:00:00"

# Enable encryption change detection
alert=ADVCRYPTCHANGE,5/min,1/sec
alert=CHANCHANGE,5/min,1/sec
alert=BEACONRATE,5/min,1/sec
```

### Monitor Web UI

1. **Devices** tab → Search "CorpWiFi"
2. Check for multiple BSSIDs with same SSID
3. Compare:
   - MAC addresses
   - Channels
   - Signal strength
   - Encryption

---

## Detection with Nzyme

### Configure Monitored Networks

In `nzyme.conf`:
```hocon
dot11.networks = [
  {
    ssid = "CorpWiFi"
    bssids = ["AA:BB:CC:DD:EE:FF"]  # Legitimate AP MAC
    channels = [6]                   # Expected channel
    security = ["WPA2-PSK"]          # Expected encryption
  }
]

dot11.traps {
  # Alert on unknown BSSID advertising our SSID
  unexpected_bssid {
    enabled = true
  }
  
  # Alert on network appearing on wrong channel
  unexpected_channel {
    enabled = true
  }
  
  # Alert on encryption mismatch
  unexpected_security {
    enabled = true
  }
  
  # Alert on fingerprint mismatch
  unexpected_fingerprint {
    enabled = true
  }
}
```

### Alert Types

| Alert | Meaning |
|-------|---------|
| `UNEXPECTED_BSSID` | Evil twin - new MAC for monitored SSID |
| `UNEXPECTED_CHANNEL` | AP on wrong channel |
| `UNEXPECTED_SECURITY` | Encryption mismatch |
| `SSID_SUBSTRING` | Typosquatting (e.g., "Corp-WiFi") |

---

## Hands-On Exercise

### Scenario
The instructor will launch an Evil Twin attack against "CorpWiFi"

### Your Task

1. **Before the attack:**
   - Note the legitimate AP's BSSID, channel, and encryption
   - Document baseline in table below

2. **During the attack:**
   - Monitor Kismet for APSPOOF alerts
   - Monitor Nzyme for UNEXPECTED_BSSID
   - Compare the evil twin's characteristics

3. **Document findings:**

#### Legitimate AP
| Field | Value |
|-------|-------|
| SSID | CorpWiFi |
| BSSID (MAC) | |
| Channel | |
| Encryption | |
| Signal (RSSI) | |

#### Evil Twin (Detected)
| Field | Value |
|-------|-------|
| SSID | CorpWiFi |
| BSSID (MAC) | |
| Channel | |
| Encryption | |
| Signal (RSSI) | |
| OUI Vendor | |

---

## Analysis Questions

1. **How did the evil twin differ from the legitimate AP?**
   - Different MAC: [ ] Yes [ ] No
   - Different channel: [ ] Yes [ ] No  
   - Different encryption: [ ] Yes [ ] No

2. **What alert triggered first?**
   - Kismet: _______________
   - Nzyme: _______________

3. **Could you identify the attack device?**
   - OUI lookup: What vendor is the evil twin's MAC?
   - Known attack device? (00:13:37 = Karma/Pineapple indicator)

4. **Which clients were at risk?**
   - Any clients probe for "CorpWiFi"?
   - Any clients connect to the evil twin?

---

## Karma Attack Detection

### What is Karma?
Karma responds to **any** probe request, creating instant evil twins for any SSID a client is looking for.

### Detection Signs
- Device advertising **many different SSIDs**
- Rapid SSID changes
- Same MAC, different SSIDs
- Look for `00:13:37:*` OUI (common in WiFi Pineapple)

### Kismet Detection
```conf
# Karma uses predictable OUI
# Alert on Karma-associated MAC prefix
alert=KARMAOUI,5/min,1/sec
```

---

## Expected Results

### Kismet Alert
```
Alert: APSPOOF
SSID: CorpWiFi
Expected MAC: AA:BB:CC:DD:EE:FF
Detected MAC: 00:13:37:XX:XX:XX  ← Evil Twin
Channel: 1 (Expected: 6)
```

### Nzyme Alert
```
Alert Type: UNEXPECTED_BSSID
Network: CorpWiFi
Expected BSSID: AA:BB:CC:DD:EE:FF
Detected BSSID: 00:13:37:XX:XX:XX
Channel: 1
Severity: CRITICAL
```

---

## Countermeasures

### Detection Improvements
- Maintain **whitelist** of legitimate AP MACs
- Monitor for **OUI anomalies** (00:13:37, random MACs)
- Alert on **encryption downgrades**
- Track **channel changes**

### Prevention
- **802.1X authentication** - Certificates prevent evil twin auth
- **EAP-TLS** - Mutual authentication
- **User awareness** - Train users to verify networks
- **VPN always-on** - Encrypt traffic regardless

### Response
1. Alert security team
2. Locate rogue device (signal triangulation)
3. Block MAC at network level if possible
4. Document for forensics

---

## Checkpoint ✅

- [ ] Identified evil twin in Kismet
- [ ] Identified evil twin in Nzyme
- [ ] Documented differences from legitimate AP
- [ ] Understand Karma attack pattern
- [ ] Know countermeasures

---

## Next Lab
→ [LAB5: Detecting Rogue APs & Advanced Attacks](LAB5-detect-rogue-ap.md)
