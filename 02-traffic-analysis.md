# 02 — Network Traffic Analysis (Wireshark)
**Tool:** Wireshark | **Date:** June 2026  
**Capture duration:** ~10 minutes  
**Interface:** eth0  
**Capture file:** home-traffic.pcap (stored locally)

---

## Protocols observed

| Protocol | Packets | What it revealed |
|---|---|---|
| ARP | Yes | All active devices on local network |
| mDNS | Yes | Device at 192.168.29.68 announcing itself |
| SSDP | Yes | UPnP service discovery from two devices |
| TLS v1.3 | Yes | Encrypted HTTPS traffic to GitHub, Google |
| ICMP | Yes | Ping traffic from Kali to 8.8.8.8 |
| IGMP v3 | Yes | Multicast group membership — normal |
| ICMPv6 | Yes | IPv6 network control traffic — normal |

---

## Devices identified

| IP | How discovered | Device |
|---|---|---|
| 192.168.29.1 | ARP | Home router |
| 192.168.29.68 | mDNS + SSDP only | Unknown device |
| 192.168.29.174 | ARP + mDNS + SSDP | Windows machine |
| 192.168.29.247 | ARP | Kali Linux VM |

---

## Key finding — device missed by Nmap

IP `192.168.29.68` was **not detected** in the Nmap 
active scan but appeared clearly in passive mDNS and 
SSDP traffic during Wireshark capture.

This demonstrates a critical analyst insight:
- Active scanning (Nmap) sends probes — some devices 
  ignore or block them
- Passive capture (Wireshark) observes traffic devices 
  generate naturally — harder to hide from

**Conclusion:** Passive traffic analysis should always 
complement active scanning for complete network visibility.

---

## External IPs observed

| IP | Owner | Traffic type |
|---|---|---|
| 8.8.8.8 | Google LLC | DNS queries (ping) |
| 20.207.73.82 | Microsoft/GitHub | TLS — HTTPS to github.com |

---

## TLS traffic observed

TLS v1.3 connections identified to:
- `github.com` — confirmed via SNI field in Client Hello packet
- `youtube.com` — confirmed via SNI field in Client Hello packet
- `google.com` — confirmed via SNI field in Client Hello packet

All traffic encrypted. Content not visible — only 
destination identifiable. This is expected and correct 
behaviour for HTTPS.

---

## Multicast addresses observed

| Address | Protocol | Purpose |
|---|---|---|
| 224.0.0.1 | IGMP | All-hosts multicast group |
| 224.0.0.22 | IGMP v3 | Multicast membership reporting |
| 224.0.0.251 | mDNS | Local device name resolution |
| 239.255.255.250 | SSDP | UPnP service discovery |

All multicast traffic is normal home network behaviour.

---

## Personal takeaways

- Wireshark passive capture revealed a device (192.168.29.68) 
  that Nmap active scan completely missed
- TLS v1.3 is now standard — content is encrypted but 
  destination IPs and domain names are still visible 
  via SNI in the Client Hello packet
- mDNS and SSDP reveal a lot about devices on a network 
  even without any active probing
- Modern home networks generate significant background 
  traffic even when no one is actively using them
