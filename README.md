# Cloud Security Monitoring & Attack Detection with Wazuh

## Overview

This project focuses on building and evaluating an open-source **Cloud Security Monitoring** solution using **Wazuh** on **AWS**.

The lab environment was designed to centralize security telemetry, monitor cloud workloads, detect suspicious activities, and investigate attack behavior through security alerts and dashboards.

The project also includes controlled attack simulations using **Nmap** and **Hydra**, custom Wazuh detection rules, attack-chain correlation, and quantitative evaluation of detection performance.

---

## Project Objectives

* Build a centralized security monitoring environment using Wazuh.
* Collect security logs from cloud workloads and AWS services.
* Monitor SSH authentication and web/server activities.
* Integrate AWS CloudTrail logs into the monitoring platform.
* Simulate common attack techniques in a controlled environment.
* Develop custom Wazuh detection and correlation rules.
* Investigate detected events through the Wazuh Dashboard.
* Measure detection rate, alert latency, and false positive rate.

---

## Architecture

The project was deployed in an AWS-based lab environment with a local Kali Linux machine used for attack simulation.

```text
                         ┌─────────────────────┐
                         │     Kali Linux      │
                         │   Attack Machine    │
                         └──────────┬──────────┘
                                    │
                             Attack Simulation
                                    │
                                    ▼
                         ┌─────────────────────┐
                         │    Victim Web EC2   │
                         │     Ubuntu 24.04    │
                         │   Wazuh Agent       │
                         │   Nginx / SSH       │
                         └──────────┬──────────┘
                                    │
                              Security Logs
                                    │
                                    ▼
                    ┌─────────────────────────────┐
                    │      Wazuh Platform         │
                    │                             │
                    │  Manager / Indexer /        │
                    │  Dashboard                  │
                    └─────────────┬───────────────┘
                                  │
                         Security Monitoring
                                  │
                                  ▼
                    ┌─────────────────────────────┐
                    │      AWS Security Logs       │
                    │                             │
                    │ CloudTrail / CloudWatch     │
                    └─────────────────────────────┘
```

### Main Components

| Component         | Purpose                                                   |
| ----------------- | --------------------------------------------------------- |
| Wazuh Manager     | Security event processing and rule evaluation             |
| Wazuh Indexer     | Security event storage and indexing                       |
| Wazuh Dashboard   | Monitoring, visualization and investigation               |
| Wazuh Agent       | Log collection from the monitored workload                |
| Victim Web Server | Target workload used for monitoring and attack simulation |
| AWS CloudTrail    | AWS API and account activity monitoring                   |
| AWS CloudWatch    | Additional cloud monitoring source                        |
| Kali Linux        | Controlled attack simulation                              |

---

## Log Sources

The monitoring environment collects security telemetry from multiple sources:

* System and SSH logs
* Nginx logs
* AWS CloudTrail
* AWS CloudWatch
* Wazuh Agent events

This provides visibility across both **host-level activity** and **cloud-level activity**.

---

## Attack Simulation

A controlled attack chain was used to validate the monitoring and detection capabilities.

### 1. Reconnaissance

Network and service discovery were performed using Nmap.

```bash
nmap -Pn -sS -sV <TARGET_IP>
```

### 2. SSH Brute Force

Hydra was used to generate repeated SSH authentication failures and validate brute-force detection.

### 3. Successful SSH Access

A successful SSH authentication event was used to validate detection of a successful compromise following repeated authentication failures.

### 4. Privilege Escalation

A controlled privilege escalation scenario using `sudo` was performed to generate and validate privilege escalation events.

### 5. Attack Chain Correlation

The individual events were correlated to identify the broader attack sequence.

```text
Reconnaissance
      │
      ▼
SSH Brute Force
      │
      ▼
Successful SSH Login
      │
      ▼
Privilege Escalation
      │
      ▼
Attack Chain Correlation
```

---

## Custom Detection Rules

Five custom Wazuh rules were developed for the lab environment.

| Rule ID | Detection                         | Level |
| ------- | --------------------------------- | ----: |
| 100010  | SSH brute-force activity          |    10 |
| 100011  | Successful SSH compromise         |    12 |
| 100012  | Port scanning / Nmap activity     |     7 |
| 100013  | Privilege escalation through sudo |    13 |
| 100014  | Full attack-chain correlation     |    15 |

The custom rule IDs use the `100000–119999` range to keep the project-specific rules separated from the default Wazuh rule set.

### Correlation Logic

The brute-force detection correlates multiple failed SSH authentication attempts within a defined time window.

The successful brute-force rule is triggered when repeated failed logins are followed by a successful login.

The attack-chain rule correlates multiple attack stages within a larger time window to provide a higher-level view of the incident.

---

## Security Monitoring Dashboard

The Wazuh Dashboard was used to monitor and investigate security events.

The monitoring view includes:

* Critical alerts
* Failed login attempts
* Successful login events
* Total alerts
* Alert severity distribution
* Attack frequency
* MITRE ATT&CK technique mapping
* Raw security events
* SSH authentication activity
* Event investigation and tracing

---

## Performance Evaluation

The detection capability was evaluated using controlled attack scenarios.

| Metric                |       Result |
| --------------------- | -----------: |
| Detection Rate        |         100% |
| Average Alert Latency | 3.33 seconds |
| False Positive Rate   |        0.93% |

### Detection Rate

The tested attack sequence contained five expected attack behaviors, all of which were successfully detected.

**Detection Rate: 5/5 = 100%**

This result applies specifically to the controlled scenarios tested in this project.

### Alert Latency

Three representative events were measured:

| Test                 | Attack Time | Alert Time | Latency |
| -------------------- | ----------- | ---------- | ------: |
| SSH Brute Force #1   | 18:05:06    | 18:05:10   |      4s |
| SSH Brute Force #2   | 18:08:17    | 18:08:22   |      5s |
| SSH Successful Login | 18:10:23    | 18:10:24   |      1s |

**Average latency: 3.33 seconds**

### False Positive Rate

During the benchmark:

* Total alerts: 108
* False positives: 1
* False positive rate: 0.93%

The observed false positive was associated with a legitimate AWS CloudTrail `ConsoleLogin` event.

---

## My Contribution

**Role: Purple Team — Attack Simulation & Detection Engineering**

My main responsibilities in this project included:

* Designing and executing controlled attack scenarios.
* Performing reconnaissance using Nmap.
* Simulating SSH brute-force attacks using Hydra.
* Executing controlled privilege escalation scenarios.
* Analyzing generated security events.
* Developing custom Wazuh detection rules.
* Tuning detection logic and correlation conditions.
* Validating detection of individual attack stages.
* Validating the complete attack-chain correlation.
* Supporting quantitative evaluation of detection performance.

The work followed a **Purple Team approach**, combining offensive attack simulation with defensive detection engineering.

---

## Technologies

* Wazuh
* AWS EC2
* AWS CloudTrail
* AWS CloudWatch
* Ubuntu Linux
* Kali Linux
* Nmap
* Hydra
* Nginx
* SSH
* XML-based Wazuh Rules
* MITRE ATT&CK

---

## Project Outcomes

The project successfully demonstrated a practical cloud security monitoring workflow:

```text
Log Collection
      ↓
Event Analysis
      ↓
Detection Rules
      ↓
Alert Generation
      ↓
Attack Correlation
      ↓
Security Investigation
      ↓
Performance Evaluation
```

The tested environment achieved:

* **100% detection rate** for the defined attack scenarios.
* **3.33 seconds average alert latency**.
* **0.93% false positive rate** during the benchmark.

These results demonstrate the feasibility of using Wazuh as a cost-effective security monitoring platform for a small cloud-based environment.

---

## Documentation

Detailed technical documentation is available in the following sections:

* [Architecture](docs/architecture.md)
* [Deployment](docs/deployment.md)
* [Attack Simulation](docs/attack-simulation.md)
* [Detection Rules](docs/detection-rules.md)
* [Performance Evaluation](docs/performance.md)

---

---

## Note

This project was developed as a controlled security laboratory environment for educational and portfolio purposes. Attack techniques were executed only against authorized lab systems.
