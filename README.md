# HealthShield — Healthcare Security Operations Platform

HealthShield is a healthcare oriented Security Operations Platform designed to collect security telemetry, correlate related events, identify suspicious activity, generate risk based incidents, and support SOC analyst investigation.

> **Know who did what, when it happened, where it happened, and why it matters.**

**Built by Kabo Sekoto**

---

## What HealthShield Demonstrates

HealthShield was built as a local security operations lab using PowerShell and Windows based security telemetry.

The platform brings multiple security domains into one incident workflow:

* Identity and authentication
* Windows endpoint telemetry
* Web application security
* Patient-data access monitoring
* Email security
* Network security
* Incident correlation
* Risk scoring
* SOC investigation
* Incident lifecycle management

The project is designed around a practical SOC principle:

> **Individual events provide evidence. Correlated events provide context.**

---

## Security Operations Workflow

```text
Telemetry
   │
   ▼
Collection
   │
   ▼
Normalisation
   │
   ▼
Correlation
   │
   ▼
Detection
   │
   ▼
Risk Scoring
   │
   ▼
Incident Creation
   │
   ▼
SOC Investigation
   │
   ▼
Containment / Resolution
```

HealthShield is therefore more than a log viewer. It attempts to connect related security events into an investigation that an analyst can understand.

---

# Core Detection Example

The strongest demonstration currently implemented in the project correlates authentication activity with sensitive healthcare-data access.

```text
5 × Failed Authentication
        │
        ▼
Successful Authentication
        │
        ▼
Patient Record Views
        │
        ▼
Patient Data Export
        │
        ▼
HealthShield Correlation Engine
        │
        ▼
CRITICAL INCIDENT
        │
        ▼
Potential Account Compromise
with Sensitive Data Exposure
        │
        ▼
Risk Score: 24
```

This represents a controlled security simulation rather than a real healthcare attack.

---

# Demonstrated Incident

HealthShield generated a critical incident during the controlled demonstration.

### Detection

**Potential Account Compromise with Sensitive Data Exposure**

### Severity

`CRITICAL`

### Risk Score

`24`

### Demonstration actor

`Nurse01`

### Demonstration endpoint

`CLINIC-WS-014`

### Demonstration sequence

```text
4625 — Failed Login
4625 — Failed Login
4625 — Failed Login
4625 — Failed Login
4625 — Failed Login
4624 — Successful Login
PATIENT_VIEW
PATIENT_VIEW
PATIENT_VIEW
PATIENT_VIEW
PATIENT_EXPORT
```

The sequence demonstrates how repeated authentication failures followed by successful authentication and sensitive patient-data activity can be correlated into a higher confidence security incident.

The generated incident contains an alert ID, severity, risk score, actor, endpoint, source information, event sequence, timeline and incident status.

---

# Detection Coverage

HealthShield contains correlation logic for multiple security scenarios.

| Detection                                       | Purpose                                                         | Severity    |
| ----------------------------------------------- | --------------------------------------------------------------- | ----------- |
| Possible Account Compromise                     | Repeated authentication failures followed by success            | HIGH        |
| Possible Privilege Escalation                   | Account creation combined with administrative privilege changes | CRITICAL    |
| Sensitive Record Access                         | Monitoring sensitive healthcare record activity                 | MEDIUM      |
| Web Application Threat Activity                 | Correlation of suspicious web indicators                        | MEDIUM/HIGH |
| Suspected Phishing Message                      | Identification of phishing-related telemetry                    | HIGH        |
| Suspicious Process Execution                    | Detection of suspicious endpoint process activity               | HIGH        |
| Endpoint Malware Detection                      | Malware-related endpoint telemetry                              | CRITICAL    |
| Network Connection Anomaly                      | Detection of abnormal blocked connection bursts                 | HIGH        |
| Account Compromise with Sensitive Data Exposure | Cross-domain identity + healthcare-data correlation             | CRITICAL    |

---

# Security Domains

## Identity Monitoring

Identity telemetry can include:

* Successful logons
* Failed logons
* Account creation
* Privilege changes
* Authentication sequences

Windows Security events such as `4624` and `4625` can be used as inputs to the correlation engine.

---

## Endpoint Security

The endpoint layer is designed to collect security telemetry such as:

* Windows authentication events
* Process activity
* PowerShell activity
* Windows Defender events
* Firewall-related activity
* Endpoint security indicators

The project includes an endpoint collector for Windows environments.

---

## Web Security

The web security layer can consume telemetry from:

* Hospital websites
* Patient portals
* Reverse proxies
* WAFs
* Application gateways
* APIs

Example security indicators include:

* Suspicious request patterns
* Authentication attacks
* Sensitive endpoint probing
* Blocked requests
* Application security indicators

Request metadata can include:

```text
Timestamp
Source IP
HTTP method
URL
Result
User agent
```

Authenticated application audit events can additionally identify the application user, resource and business action.

---

# Patient Data Security

Healthcare systems contain highly sensitive information.

HealthShield therefore treats patient-data activity as an important security telemetry source.

Supported business actions include:

```text
VIEW
UPDATE
EXPORT
DELETE
PRINT
SHARE
```

The security platform is intended to record **audit metadata rather than medical content**.

This allows the SOC to investigate potentially suspicious access without unnecessarily copying clinical information into the security platform.

---

# Email Security

Email telemetry can be used to identify security indicators such as:

* Phishing links
* Suspicious messages
* Quarantined messages
* Sender information
* User interaction indicators

The demonstration environment includes a controlled phishing event.

---

# Network Security

Network telemetry can provide visibility into:

* Connection attempts
* Source and destination addresses
* Blocked connections
* Destination ports
* Connection bursts
* Firewall activity

The demonstration environment includes controlled blocked network connection events.

---

# Correlation Engine

A major design goal of HealthShield is to avoid treating every individual event as a separate incident.

For example:

```text
5 Failed Logins
       +
1 Successful Login
       +
Patient Record Access
       +
Patient Export
       =
Higher-confidence security incident
```

The correlation engine evaluates relationships between events and produces:

* Detection name
* Severity
* Risk score
* Actor
* Source
* Endpoint
* Event sequence
* Timeline
* Incident status
* Investigation context

This approach provides more useful SOC context than simply displaying isolated logs.

---

# Risk Scoring

HealthShield assigns risk scores to detections.

Example:

```text
Detection:
Potential Account Compromise
with Sensitive Data Exposure

Risk Score:
24

Severity:
CRITICAL
```

Severity is then classified from the calculated risk score.

```text
CRITICAL
HIGH
MEDIUM
LOW
```

This gives the analyst a prioritisation mechanism for deciding which incidents require immediate investigation.

---

# Incident Lifecycle

Generated incidents can move through an analyst workflow:

```text
NEW
 │
 ▼
INVESTIGATING
 │
 ▼
CONTAINED
 │
 ▼
RESOLVED
```

The incident record preserves the investigation context rather than treating the alert as a disposable notification.

---

# Live Command Centre

HealthShield includes a local command centre for monitoring the security environment.

The dashboard provides visibility into areas such as:

* Critical incidents
* High incidents
* Medium incidents
* Open incidents
* Events processed
* Identity telemetry
* Web telemetry
* Endpoint telemetry
* Email telemetry
* Network telemetry
* Incident queue
* Detection coverage
* Analyst incident status

The local dashboard runs on:

```text
http://127.0.0.1:8766/
```

The central event API runs on:

```text
http://127.0.0.1:8780
```

---

# Architecture

```text
                    ┌─────────────────────┐
                    │ Windows Endpoint    │
                    │ Security Telemetry  │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ HealthShield Agent   │
                    └──────────┬──────────┘
                               │
                               │
        ┌──────────────────────┼──────────────────────┐
        │                      │                      │
        ▼                      ▼                      ▼
   Web Events             Email Events          Network Events
        │                      │                      │
        └──────────────────────┼──────────────────────┘
                               │
                               ▼
                 ┌──────────────────────────┐
                 │ HealthShield Central     │
                 │ Collector / Correlator   │
                 └────────────┬─────────────┘
                              │
                              ▼
                 ┌──────────────────────────┐
                 │ Detection & Risk Engine  │
                 └────────────┬─────────────┘
                              │
                              ▼
                    ┌─────────────────────┐
                    │ Incident Generation │
                    └──────────┬──────────┘
                               │
                               ▼
                    ┌─────────────────────┐
                    │ SOC Command Centre  │
                    └─────────────────────┘
```

---

# Project Structure

```text
HealthShield-Healthcare-Security-Operations-Platform/
│
├── collectors/
│   ├── HealthShield-Agent.ps1
│   └── HealthShield-Central.ps1
│
├── connectors/
│   ├── HealthShield-Demo.ps1
│   ├── HealthShield-Web.ps1
│   ├── HealthShield-ApplicationAudit.ps1
│   └── examples/
│
├── config/
│   └── HealthShield.json
│
├── dashboard/
│   └── healthshield.svg
│
├── detectors/
│
├── docs/
│   ├── DEMO-RUNBOOK.md
│   ├── DEPLOYMENT.md
│   ├── DETECTION-CATALOGUE.md
│   ├── PRODUCT-ARCHITECTURE.md
│   ├── PRODUCTION-ROADMAP.md
│   ├── SECURITY-BOUNDARIES.md
│   └── WEB-SECURITY.md
│
├── evidence/
│
├── HospitalData/
│
├── incidents/
│
├── logs/
│
├── runtime/
│
├── CHECK-HEALTHSHIELD.cmd
├── CHECK-HEALTHSHIELD.ps1
├── RUN-DEMO.cmd
├── START-HEALTHSHIELD.cmd
├── START-HEALTHSHIELD.ps1
├── STOP-HEALTHSHIELD.cmd
├── STOP-HEALTHSHIELD.ps1
│
├── PRODUCT-BRIEF.md
└── README.md
```

---

# Running the Demonstration

This project is designed for a controlled local environment.

### 1. Start HealthShield

Run:

```powershell
.\START-HEALTHSHIELD.cmd
```

The central collector starts the local security platform.

### 2. Verify the platform

Run:

```powershell
.\CHECK-HEALTHSHIELD.cmd
```

### 3. Submit the controlled security demonstration

Run:

```powershell
.\RUN-DEMO.cmd
```

The demonstration generates synthetic telemetry covering:

```text
Authentication failures
Successful authentication
Web security events
Patient record access
Patient data export
Phishing telemetry
Blocked network connections
```

The events are submitted to the local HealthShield API.

---

# Demonstration Safety

The demonstration does **not** attack a real hospital, website or external system.

The project uses synthetic security events and documentation-reserved network addresses for controlled testing.

Example demonstration addresses include:

```text
203.0.113.45
198.51.100.23
198.51.100.44
198.51.100.55
```

These are used as simulated sources inside the lab.

No real patient information is required for the demonstration.

---

# Evidence

The repository contains evidence supporting the development and operation of the platform.

Evidence can include:

* Dashboard views
* Generated incident records
* Detection results
* Controlled demonstration output
* Architecture documentation
* SOC investigation material

The strongest demonstrated case is the critical account-compromise/data-exposure correlation described above.

---

# What This Project Demonstrates

This project demonstrates practical ability in:

* Security telemetry collection
* Windows security event analysis
* Authentication monitoring
* Event correlation
* Detection engineering
* Risk scoring
* Incident generation
* SOC investigation
* Healthcare security monitoring
* PowerShell automation
* Security dashboard development
* Incident documentation
* Controlled security testing

The project also demonstrates the ability to move from individual security events to an analyst focused incident narrative.

---

# Latest Release Candidate Validation

On 9 October 2026, the local release demonstration was checked with the following results:

| Check | Result |
| --- | --- |
| Release folder and ZIP archive created | PASS |
| Required startup, check, collector, README and dashboard files present in the release folder | PASS |
| Dashboard opened in the browser | PASS |
| Local HealthShield API responded with status `ONLINE` | PASS |
| Synthetic web security event submitted through the release copy's web connector | PASS |
| API returned a `Web Application Threat Activity` alert with severity `HIGH`, risk score `12`, actor `HealthShieldFreshTest`, and status `NEW` | PASS |

The web event was synthetic training data. This validates the observed local demonstration path; it does not establish that every detector has been tested or that the release runs independently of any other local HealthShield process.

# Project Limitations

HealthShield is a local cybersecurity laboratory and portfolio project.

It should not be represented as a production ready hospital SIEM/SOC platform.

A production deployment would require additional controls including:

* Secure endpoint enrolment
* Mutual authentication
* Encrypted communications
* Role-based access control
* Central durable storage
* High availability
* Secrets management
* Audit protection
* Threat intelligence integration
* Behavioural analytics
* Production-grade access controls
* Formal incident-response integration

These areas are documented in the project's production roadmap.

---

# Responsible Use

Testing against any production healthcare environment requires written authorisation, defined scope and approved rules of engagement.

HealthShield's demonstration environment is designed to exercise the security workflow safely using synthetic telemetry.

---

# Author

**Kabo Sekoto**

Product Owner | Detection Engineering

---

## Project Status

**Working local SOC demonstration**

The current implementation successfully demonstrates:

```text
Telemetry Collection
        ↓
Event Correlation
        ↓
Detection
        ↓
Risk Scoring
        ↓
Critical Incident Generation
        ↓
SOC Investigation
```

**HealthShield — turning security telemetry into an investigation.**
