# AWS Cloud Honeypot & Real-Time Threat Intelligence Pipeline

- **Security Engineer:** Harmandeep Singh
- **Focus Area:** Cloud Security, Threat Intelligence, SOC Automation, Python
- **Target Roles:** Tier-1 SOC Analyst | Junior Cloud Security Analyst | Security Operations Engineer

---

## 1. Executive Summary
Designed and deployed an intentional decoy cloud honeypot on an AWS EC2 instance (`t3.micro`) exposed to the public internet (`0.0.0.0/0` on Port 22). Engineered a real-time Python monitoring engine that intercepts inbound authentication attempts, extracts Indicators of Compromise (IoCs), enriches attacker IPs with real-time geolocation intelligence (Country, City, ISP/ASN), and outputs automated alerts to an audit log (`threat_intel.json`).

---

## 2. Architecture & Intelligence Pipeline

```text
  [ Global Threat Actors / Scanners ]
               │
               ▼ (Port 22 SSH Probes)
  ┌────────────────────────────────────────────────────────┐
  │  AWS Public Subnet: 3.106.244.130                      │
  │  Security Group: honeypot-public-sg (0.0.0.0/0)        │
  │                                                        │
  │  [ Ubuntu EC2 Honeypot: threat_monitor.py ]            │
  │   • Intercepts raw systemd/sshd authentication events  │
  │   • Parses attacker IP addresses via Regex             │
  │   • Queries REST Geolocation API (ip-api.com)          │
  └────────────────────────────┬───────────────────────────┘
                               │
               ┌───────────────┴───────────────┐
               ▼                               ▼
    [ Live Console Alerts ]        [ Structured Intelligence ]
     • IP, Location, ISP            • threat_intel.json
     • Attack Timestamps            • IoC Ingestion Pipeline
---
## 3. Real Captured Attack Telemetry

### Incident 1: External Automated Perimeter Probe
- **Attacker IP:** `195.182.22.227`
- **Location:** Perehinske, Ukraine 🇺🇦
- **Autonomous System / ISP:** `Netgroup-Kalush Ltd.`
- **Targeted Port:** TCP 22 (SSH)
- **Timestamp:** `Wed Nov 18 10:06:58 2026`
- **Analysis:** Automated scanning script probing public AWS IP space; connection blocked by public key enforcement and logged to `threat_intel.json`.

### Incident 2: Controlled Telemetry Validation Test
- **Source IP:** `157.39.220.52`
- **Location:** Batāla, India 🇮🇳
- **ISP:** `Reliance Jio Infocomm Limited`
- **Targeted Port:** TCP 22 (SSH)
- **Timestamp:** `Wed Nov 18 10:03:47 2026`
- **Analysis:** Simulated authentication probe executed via external PowerShell client; confirmed real-time regex parsing, geolocation resolution, and JSON serialization.
