# 03 — IP Reputation Analysis (Threat Intelligence)

**Tools:** VirusTotal, AbuseIPDB  
**Date:** July 2026

---

## Objective

Investigate external IP addresses observed during network traffic analysis to determine whether they are associated with malicious activity or legitimate services.

---

## Methodology

External IP addresses identified during Wireshark packet capture were investigated using multiple threat intelligence sources.

- **VirusTotal** — Checked IP reputation across 90+ security vendors.
- **AbuseIPDB** — Verified community-reported abuse history.
- **WHOIS / ASN Information** — Confirmed ownership of the IP addresses.
- **SSL Certificate Details** — Used as supporting evidence where applicable.

---

## Investigated IP Addresses

| IP Address | Organization | VirusTotal | AbuseIPDB | Verdict |
|------------|--------------|------------|------------|---------|
| `20.207.73.82` | Microsoft Corporation (GitHub) | 0/94 detections | 0% Abuse Confidence | ✅ Legitimate |
| `8.8.8.8` | Google LLC (Public DNS) | 0/94 detections | 0% Abuse Confidence | ✅ Legitimate |

---

## Investigation Details

### 1. GitHub Infrastructure (Microsoft)

**Observed IP:** `20.207.73.82`

**Traffic Source**
- Manually initiated `curl https://github.com` from the Kali Linux VM during packet capture.

**Analysis**
- VirusTotal reported **0/94 security vendor detections**.
- AbuseIPDB reported **0% Abuse Confidence Score**.
- WHOIS/ASN lookup identified the IP as **AS8075 (Microsoft Corporation)**.
- SSL certificate confirmed the endpoint as **CN=github.com** (issued by Sectigo).

**Verdict**

The observed traffic was legitimate outbound HTTPS communication with GitHub infrastructure. No indicators of compromise were identified.

---

### 2. Google Public DNS

**Observed IP:** `8.8.8.8`

**Traffic Source**
- ICMP echo request (ping) generated from the Kali Linux VM.

**Analysis**
- VirusTotal reported **0/94 detections**.
- AbuseIPDB reported **0% Abuse Confidence Score**.
- IP belongs to **Google LLC** and hosts the Google Public DNS service.

**Verdict**

Expected network traffic to a trusted public DNS service. No malicious activity observed.

---

## Summary

- Investigated **2 external IP addresses** identified during packet analysis.
- Verified ownership using WHOIS/ASN information.
- Cross-referenced IP reputation using VirusTotal and AbuseIPDB.
- Both IPs belonged to trusted organizations and showed **no indicators of compromise (IOCs)**.
- The captured network traffic was determined to be legitimate.

---

## SOC Analyst Workflow

This exercise follows a common Tier 1 SOC investigation process:

1. Capture network traffic using Wireshark.
2. Identify external IP addresses.
3. Check IP reputation using VirusTotal.
4. Validate abuse history using AbuseIPDB.
5. Verify ownership through WHOIS/ASN information.
6. Correlate findings and document the final verdict.

---

## Skills Demonstrated

- Network Traffic Analysis
- Threat Intelligence
- IP Reputation Analysis
- IOC Validation
- WHOIS / ASN Investigation
- Documentation and Reporting

---

## Personal Takeaways

- Always verify suspicious IP addresses using **multiple threat intelligence sources** rather than relying on a single platform.
- Legitimate infrastructure (e.g., GitHub or Google) can appear in packet captures and should be validated before drawing conclusions.
- SSL certificates, WHOIS records, and ASN ownership provide valuable context during IP investigations.
- Even when no malicious activity is found, documenting the investigation process is an essential SOC analyst skill.
