# 01 — Network Enumeration (Nmap)
**Tool:** Nmap 7.94SVN | **Date:** 22 June 2026  
**Command:** `nmap -sV -sC <network-range> -oN nmap-home-scan.txt`  
**Network scanned:** Home lab network (private IPv4 subnet — redacted)  
**Total hosts discovered:** 7 out of 256 IPs

---

## Devices Found

### Host A — Home Router (Jio Fiber)
| Port | State | Service | Details |
|---|---|---|---|
| 53 | Open | DNS | dnsmasq 2.45 |
| 80 | Open | HTTP | lighttpd — web admin panel |
| 443 | Open | HTTPS | lighttpd — SSL admin panel |
| 7443 | Open | HTTPS | Jio management service |
| 8080 | Open | HTTP-proxy | Jio gateway service |
| 8443 | Open | HTTPS | Jio gateway service |
| 22 | Closed | SSH | — |

**Analysis:**  
Router is running a DNS service and multiple web management 
interfaces. HTTP (port 80) is open — the admin panel is 
accessible unencrypted on the local network.  

**Finding:** Port 80 exposes the router admin interface over 
unencrypted HTTP. An attacker on the same network could 
intercept login credentials using a traffic capture tool.  
**Recommendation:** Disable HTTP access to admin panel. 
Use HTTPS (port 443) only.

---

### Host B — Samsung Smart TV
| Port | State | Service | Details |
|---|---|---|---|
| 7000 | Open | RTSP | AirTunes — media streaming |
| 8001 | Open | HTTP | SmartView SDK service |
| 8002 | Open | HTTPS | SmartView SDK (SSL) |
| 8080 | Open | HTTP | WebServer — 403 Forbidden |

**Analysis:**  
Samsung Smart TV running AirPlay streaming and SmartView 
remote control services. SSL certificate issued to 
"SmartViewSDK" — confirms Samsung TV identity.  

**Finding:** Smart TV exposes 4 network ports for streaming 
and remote control. These are expected for TV functionality 
but increase the network attack surface.  
**Recommendation:** Keep TV on a separate IoT network segment 
where possible. Ensure TV firmware is up to date.

---

### Host C — Android Device
| Port | State | Service | Details |
|---|---|---|---|
| 2869 | Open | HTTP | Android AccessTwine — DLNA media server |

**Analysis:**  
Android device running a DLNA media sharing service 
(AccessTwine). Device model fingerprint: JHSC200.  
Likely an Android TV box or streaming device.

**Finding:** Low risk. DLNA sharing is a standard feature 
but unnecessarily exposes the device on the network if 
media sharing is not actively used.  
**Recommendation:** Disable DLNA/media sharing if not needed.

---

### Host D — Windows Machine
| Port | State | Service | Details |
|---|---|---|---|
| 135 | Open | MSRPC | Microsoft Windows RPC |
| Multiple high ports | Closed | Unknown | 49152–65389 range |

**Analysis:**  
Windows device confirmed by OS detection and open RPC port.  
Port 135 (Windows RPC) is standard on Windows systems and 
is used for remote procedure calls between Windows services.

**Finding:** Low risk in a home environment. However, port 135 
has historically been targeted in Windows worm attacks 
(e.g. Blaster worm, MS03-026). Should never be exposed to 
the internet.  
**Recommendation:** Ensure Windows Firewall is active and 
port 135 is not exposed externally.

---

### Host E — Unknown Device
| Port | State | Service |
|---|---|---|
| All 1000 ports | Closed | — |

**Analysis:**  
Device is alive on the network (responding to ping) but has 
no open TCP ports detected. Could be a phone, smart home 
device, or printer in a sleep state.  

**Finding:** Informational only — no open services detected.

---

### Host F — Unknown Device (filtered)
| Port | State | Service |
|---|---|---|
| 7 | Filtered | Echo |

**Analysis:**  
Single filtered port detected. Firewall is actively blocking 
probes. Device identity unclear from scan alone.  
Likely a mobile phone or smart device.

**Finding:** Low risk. Filtered port means firewall is 
working correctly on this device.

---

### Host G — Kali Linux VM (analyst workstation)
| Port | State | Service | Details |
|---|---|---|---|
| 21 | Open | FTP | vsftpd 3.0.5 |
| 22 | Open | SSH | OpenSSH 9.9p1 Debian 3 |

**Analysis:**  
This is the scanning machine itself — Kali Linux running 
inside VirtualBox.

**Finding — FTP (port 21) open — Medium severity:**  
FTP transmits all data including credentials in plain text 
with zero encryption. Any device on the same network 
capturing traffic with Wireshark could intercept FTP 
usernames and passwords in seconds.  
**Recommendation:** Disable FTP service immediately. 
Use SFTP instead — it runs over SSH (port 22) and is 
fully encrypted.

```bash
# To stop FTP service on Kali:
sudo service vsftpd stop

# To disable it permanently:
sudo systemctl disable vsftpd
```

**Finding — SSH (port 22) open — Low severity:**  
SSH is expected on a Kali Linux machine for remote 
administration. Acceptable in a home lab environment.  
**Recommendation:** Ensure strong password or key-based 
authentication is configured.

---

## Findings Summary

| Host | Device | Port | Severity | Finding |
|---|---|---|---|---|
| Host A | Jio Router | 80 | Medium | HTTP admin panel unencrypted |
| Host B | Samsung TV | 7000, 8001 | Info | Media services exposed |
| Host C | Android device | 2869 | Info | DLNA sharing enabled |
| Host D | Windows machine | 135 | Low | RPC — standard but notable |
| Host E | Unknown | — | Info | No open ports |
| Host F | Unknown | 7 filtered | Info | Firewall active |
| Host G | Kali Linux VM | 21 | Medium | FTP open — unencrypted protocol |
| Host G | Kali Linux VM | 22 | Low | SSH open — acceptable |

---

## Key Observations

1. **Most significant finding:** FTP running on the Kali machine 
   is an unencrypted file transfer service — a real vulnerability 
   in any network environment.

2. **Router HTTP interface:** Admin panel accessible over HTTP 
   means credentials could be captured in plaintext on the 
   local network.

3. **IoT attack surface:** Smart TV and Android device add 
   multiple open ports to the network. IoT devices are 
   frequently under-patched and rarely monitored.

4. **No external threats detected:** All discovered devices 
   are known home network devices. No unauthorized or 
   unexpected hosts found.

---

## What I learned from this scan

- Nmap `-sV` detects service versions — useful for identifying 
  outdated software with known CVEs
- Nmap `-sC` runs default scripts that reveal extra detail 
  like SSL certificates and service banners
- A home network has more exposed services than most people 
  realize — router, TV, and phone all expose ports
- FTP and HTTP are legacy unencrypted protocols — modern 
  equivalents (SFTP, HTTPS) should always be preferred
- Identifying a device from its MAC address vendor and 
  service fingerprint is a real analyst skill

---

## Screenshots
<img width="942" height="832" alt="nmap_scan" src="https://github.com/user-attachments/assets/a6b3212f-d3cb-481f-9dd6-4091b942b215" />



## Raw output
Stored locally as `nmap-home-scan.txt` — not published 
publicly to protect network details.
