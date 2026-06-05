=========================
⚠️ Disclaimer

This project is intended for educational and research purposes only.

All activities demonstrated in this lab (including attack simulations, Active Directory exploitation techniques, and endpoint compromise scenarios) are conducted in a controlled and isolated environment.

No part of this project is designed to target real systems or organizations without explicit authorization.

The author does not promote or condone any illegal activities. Any misuse of the information provided in this repository is the sole responsibility of the user.

By using this content, you agree to apply these concepts ethically and in compliance with applicable laws and regulations.

=========================


# SOC Zero Trust Lab

## Overview

This project consists of designing and implementing a **SOC (Security Operations Center) lab** integrating a **multi-layer Zero Trust architecture**.

The goal is to reproduce realistic scenarios (Active Directory attacks, suspicious behavior, endpoint compromise) and demonstrate the ability to:

* collect and centralize security logs
* analyze and correlate events
* detect malicious behaviors
* investigate security incidents
* implement a modern Zero Trust architecture

---

## Lab Architecture

### Components

* Kali Linux (attacker machine)
* Windows Server (Active Directory)
* Windows (user endpoint)
* Splunk Enterprise (SIEM)
* Splunk Universal Forwarder (log collection)
* OpenEDR Trial (EDR – endpoint detection) - Elastic-EDR-Trial***
* Velociraptor ( Hunt-Forensic )
* Crowdstrike Trial (Threat Hunting)
* Octelium (ZTNA – Zero Trust access control)

---

### Data Flow

Kali → Attack simulation → Windows → Logs → Forwarder → Splunk

User → Authentication → Octelium → Controlled access to resources

OpenEDR → Endpoint detection → Logs → Splunk

---

## Multi-layer Zero Trust Model

The lab implements a Zero Trust architecture based on multiple complementary layers.

---

### 1. Network Zero Trust (ZTNA)

Implemented with Octelium

* Identity-based access to resources
* Services are not exposed to the network
* Removal of direct access (RDP, SMB, etc.)
* Granular access control

Objective: drastically reduce the network attack surface

---

### 2. Endpoint Zero Trust

Implemented with OpenEDR

* Default deny principle
* Automatic containment of unknown files
* Process monitoring
* Behavioral detection

Objective: prevent malicious code execution even after successful access

---

### 3. SOC Visibility & Correlation

Implemented with Splunk

* Log centralization
* Event correlation
* Anomaly detection
* Security investigation

Objective: detect and respond to incidents

---

### Applied Principles

* Never trust, always verify
* Least privilege
* Assume breach
* Continuous verification

---

## Technical Stack

* Splunk Enterprise
* Splunk SOAR
* Splunk Universal Forwarder
* OpenEDR
* Octelium (ZTNA)
* Active Directory
* Windows Event Logs
* Kali Linux

---

## SIEM Deployment

### Splunk

* Installation of Splunk Enterprise
* Enable receiving on port 9997
* Index creation
* Ingestion verification

---

### Log Collection

* Install Forwarder on Windows
* Configure log forwarding
* Enable Windows security logs

---

### Monitored Events

* 4624 → Successful authentication
* 4625 → Failed authentication
* 4771 → Kerberos failure
* 4768 → Kerberos ticket request

---

## EDR Integration (OpenEDR)

### Objective

Monitor endpoints and detect suspicious behavior.

### Features

* Process monitoring
* Behavioral detection
* Security alerts
* Activity analysis

### Use Cases

* Suspicious batch scripts
* PowerShell activity
* Process loops
* Authentication attempts

---

## Zero Trust Integration (Octelium)

### Objective

Implement network access control based on the Zero Trust model.

### Features

* Authentication before access
* Network service cloaking
* Identity-based access
* Connection logging

### Use Cases

* Blocking network scans
* Eliminating exposed ports
* Secure access to internal resources

---

## Security Scenarios

---

### Scenario 1 – Active Directory Brute Force

Simulation of a password spraying attack.

Tool: Kerbrute

Generated logs:

* 4625 → failures
* 4624 → success
* 4771 → Kerberos errors

#### SPL Detection

```spl
index=* EventCode=4625
| stats count by Account_Name, src_ip
| where count > 5
```

#### Advanced Detection

```spl
index=* (EventCode=4625 OR EventCode=4624)
| stats count(eval(EventCode=4625)) as fails
        count(eval(EventCode=4624)) as success
        by Account_Name, src_ip
| where fails > 5 AND success > 0
```

---

### Scenario 2 – Network Scan (Zero Trust Impact)

Test: Nmap from Kali

Results:

* Before ZTNA → services visible
* After ZTNA → services invisible

---

### Scenario 3 – Unauthorized Access

* Direct access attempt to resources
* Access denied without authentication
* Logs generated

---

### Scenario 4 – Legitimate Access

* Access via Octelium
* Authentication required
* Full traceability

---

### Scenario 5 – Behavioral Detection

Simulated script:

```bat
@echo off
:loop
start notepad.exe
goto loop
```

Detection:

* massive process creation
* abnormal activity
* OpenEDR alert

---

## Results

* Functional log pipeline
* Centralized event collection
* AD attack detection
* SIEM + EDR + ZTNA integration
* Reduced attack surface
* Full visibility on activities

---

## Future Improvements

### Attacks

* Phishing
* Data exfiltration
* Offensive PowerShell
* Lateral movement
* Ransomware simulation

### SOC

* Advanced dashboards
* Event correlation
* Automated alerting
* Threat hunting
* MITRE ATT&CK integration

---

## Skills Developed

* SIEM deployment
* Log collection and analysis
* Attack detection
* Security investigation
* Endpoint monitoring
* Zero Trust implementation
* Behavioral analysis
* Active Directory
* Threat detection

---

## Conclusion

This project demonstrates the implementation of a modern cybersecurity architecture based on:

* realistic attack simulations
* log analysis and correlation
* SIEM and EDR integration
* a multi-layer Zero Trust model

This lab reproduces real-world scenarios and helps develop practical defensive cybersecurity skills.
