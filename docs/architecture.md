# Architecture

## Overview

The project uses an AWS-based security monitoring architecture centered around Wazuh.

The architecture combines workload-level telemetry collected through the Wazuh Agent with AWS security and monitoring services such as CloudTrail and CloudWatch.

A local Kali Linux system is used as the controlled attack source.

---

## High-Level Architecture

```text
                       ┌───────────────────┐
                       │    Kali Linux     │
                       │  Attack Machine   │
                       └─────────┬─────────┘
                                 │
                           Attack Traffic
                                 │
                                 ▼
                       ┌───────────────────┐
                       │   Victim Web EC2  │
                       │    Ubuntu 24.04   │
                       │                   │
                       │  Wazuh Agent      │
                       │  Nginx / SSH      │
                       └─────────┬─────────┘
                                 │
                              Logs
                                 │
                                 ▼
                  ┌────────────────────────────┐
                  │       Wazuh Platform       │
                  │                            │
                  │  Manager                   │
                  │  Indexer                   │
                  │  Dashboard                 │
                  └─────────────┬──────────────┘
                                │
                                │
                 ┌──────────────┴──────────────┐
                 │                             │
                 ▼                             ▼
        ┌─────────────────┐          ┌─────────────────┐
        │  AWS CloudTrail │          │ AWS CloudWatch  │
        │                 │          │                 │
        │ API / Account   │          │ Cloud Monitoring│
        │ Activity Logs   │          │ Logs            │
        └─────────────────┘          └─────────────────┘
```

---

## Infrastructure

### Wazuh Environment

The Wazuh environment was deployed on AWS EC2 using Ubuntu 24.04 LTS.

The laboratory deployment used:

* 2 vCPU
* 8 GiB RAM
* 50 GiB gp3 storage

The environment was configured as a Wazuh monitoring platform consisting of the Manager, Indexer and Dashboard components.

### Victim Web Server

The monitored workload was deployed on AWS EC2.

Configuration used in the laboratory:

* Ubuntu 24.04 LTS
* 2 vCPU
* 2 GiB RAM
* 30 GiB gp3 storage
* Wazuh Agent
* Nginx
* SSH

The Wazuh Agent forwards security events from the workload to the Wazuh platform.

---

## Attack Source

Kali Linux was used as the controlled attack machine.

The attack machine generated security events through:

* Nmap reconnaissance
* SSH brute-force attempts using Hydra
* Successful SSH authentication
* Controlled privilege escalation

---

## Log Flow

The monitoring workflow can be summarized as:

```text
AWS / Host Activity
       │
       ▼
Log Sources
       │
       ├── SSH / System Logs
       ├── Nginx Logs
       ├── CloudTrail
       └── CloudWatch
       │
       ▼
Wazuh Agent / Integration
       │
       ▼
Wazuh Manager
       │
       ▼
Detection Rules
       │
       ▼
Wazuh Indexer
       │
       ▼
Wazuh Dashboard
       │
       ▼
Security Investigation
```

---

## AWS CloudTrail Integration

AWS CloudTrail was integrated as an additional cloud-level security telemetry source.

The integration retrieves CloudTrail data stored in Amazon S3.

The configured access policy uses:

```text
s3:ListBucket
s3:GetObject
```

The Wazuh integration polls the configured S3 location periodically to retrieve CloudTrail events.

This allows AWS account and API activity to be analyzed together with host-level security events.

---

## Security Monitoring Scope

The architecture provides visibility across two main layers:

### Host Layer

* SSH authentication
* System activity
* Nginx activity
* Privilege escalation events

### Cloud Layer

* AWS API activity
* AWS account activity
* CloudTrail events
* CloudWatch monitoring data

Combining these sources provides a broader security monitoring view than monitoring the workload alone.
