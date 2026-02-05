# Lab 3: Detecting Deauthentication Attacks

**Duration:** 20 minutes  
**Objective:** Detect deauthentication and disassociation DoS attacks

---

## Attack Overview

Deauthentication attacks exploit the 802.11 management frame vulnerability:
- Management frames are **not encrypted** (even on WPA2/WPA3 networks)
- Attackers can **spoof** deauth frames
- Clients disconnect from the AP
- Used for: DoS, capturing WPA handshakes, forcing reconnection to evil twin

### Attack Tools (Instructor Demo)
- `aireplay-ng` - Deauth injection
- `mdk4` - Multi-attack tool
- WiFi Pineapple - Automated deauth
- Flipper Zero - BLE/WiFi deauth

---

## Detection with Kismet

### Relevant Kismet Alerts

| Alert | Description |
|-------|-------------|
| `DEAUTHFLOOD` | High volume of deauth frames |
| `BCASTDISCON` | Broadcast deauth/disassociation |
| `DISASSCTRAFFIC` | Disassociated client still sending data |

### Enable Alerts in kismet_site.conf

```conf
# /etc/kismet/kismet_site.conf

# Deauth flood detection
alert=DEAUTHFLOOD,10/min,5/sec

# Broadcast disconnect detection  
alert=BCASTDISCON,10/min,5/sec

# Disassociated traffic (indicator of attack)
alert=DISASSCTRAFFIC,10/min,5/sec
```

### Monitor in Web UI

1. Go to **Alerts** tab
2. Filter by: `deauth` or `disassoc`
3. Watch for:
   - Spike in alert count
   - Source MAC (attacker)
   - Target MAC (victim)
   - Target BSSID (AP)

---

## Detection with Nzyme

### Configure Deauth Monitor

In `nzyme.conf`:
```hocon
dot11.traps.deauth_monitor {
  enabled = true
  threshold = 10       # Alert after 10 frames
  window_seconds = 60  # Within 60 second window
}
```

### Alert Types

| Alert | Meaning |
|-------|---------|
| `DEAUTH_FLOOD` | Excessive deauth frames detected |
| `DISASSOC_FLOOD` | Excessive disassociation frames |
| `DEAUTH_BROADCAST` | Broadcast deauth (affects all clients) |

### View in Dashboard

1. Navigate to **Alerts**
2. Look for deauth-related events
3. Details include:
   - Timestamp
   - Frame count
   - Source MAC
   - Target MAC / Broadcast
   - Channel

---

## Hands-On Exercise

### Scenario
The instructor will launch a deauthentication attack against the lab AP.

### Your Task

1. **Monitor Kismet:**
   - Watch the Alerts tab
   - Note when `DEAUTHFLOOD` triggers
   - Identify the source MAC

2. **Monitor Nzyme:**
   - Watch the Alerts dashboard
   - Note the frame count and timing
   - Compare with Kismet detection

3. **Document findings:**

| Field | Value |
|-------|-------|
| Attack Start Time | |
| Source MAC | |
| Target AP (BSSID) | |
| Frame Count | |
| Detection Tool | |
| Alert Type | |

---

## Analysis Questions

1. **How quickly did the attack get detected?**
   - Kismet: ___ seconds
   - Nzyme: ___ seconds

2. **What was the attack pattern?**
   - [ ] Targeted (specific client)
   - [ ] Broadcast (all clients)
   - [ ] Continuous flood
   - [ ] Burst attacks

3. **Can you identify the attacker's MAC address?**
   - Note: Attackers often use spoofed/randomized MACs
   - Check OUI lookup: Is it a known vendor?

4. **What would be your incident response?**
   - Locate the physical attacker
   - Enable MFP/802.11w if supported
   - Document for forensics

---

## Countermeasures

### Short Term
- **Locate attacker** using signal strength triangulation
- **Alert security** team
- **Document** attack details

### Long Term
- **Enable 802.11w (MFP)** - Management Frame Protection
- **Use WPA3** - Includes mandatory MFP
- **Deploy WIPS** - Wireless Intrusion Prevention
- **Physical security** - Locate and remove rogue devices

### Kismet Signal Tracking
```bash
# Kismet can show signal strength to help locate attacker
# In web UI: Click on device → Signal History
# Higher RSSI = Closer to attacker
```

---

## Expected Results

When the attack is launched, you should see:

### Kismet
```
Alert: DEAUTHFLOOD
Source: XX:XX:XX:XX:XX:XX
Target: FF:FF:FF:FF:FF:FF (Broadcast)
BSSID: [Lab AP MAC]
Channel: 6
Deauth frames in last 10 seconds: 150
```

### Nzyme
```
Alert Type: DEAUTH_FLOOD
Severity: HIGH
Frame Count: 150
Window: 10 seconds
Source: XX:XX:XX:XX:XX:XX
```

---

## Checkpoint ✅

- [ ] Observed deauth attack in Kismet alerts
- [ ] Observed deauth attack in Nzyme alerts
- [ ] Identified source MAC address
- [ ] Documented attack timeline
- [ ] Understand countermeasures

---

## Next Lab
→ [LAB4: Detecting Evil Twin Attacks](LAB4-detect-evil-twin.md)
