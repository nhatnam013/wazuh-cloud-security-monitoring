# Deployment

## Deployment Overview

The security monitoring environment was deployed using AWS EC2 and Ubuntu Linux.

The deployment consists of:

* Wazuh monitoring platform
* Victim web server
* Wazuh Agent
* AWS CloudTrail integration
* AWS CloudWatch monitoring
* Kali Linux attack machine

---

## 1. Wazuh Platform

The Wazuh environment was deployed on an AWS EC2 instance running Ubuntu 24.04 LTS.

Laboratory resources:

```text
CPU:      2 vCPU
Memory:   8 GiB
Storage:  50 GiB gp3
OS:       Ubuntu 24.04 LTS
```

The Wazuh environment provides:

```text
Wazuh Manager
      │
      ├── Event processing
      ├── Rule evaluation
      └── Alert generation
      │
      ▼
Wazuh Indexer
      │
      └── Security event storage
      │
      ▼
Wazuh Dashboard
      │
      └── Monitoring & investigation
```

The laboratory configuration used 2 vCPU for the Wazuh server. This configuration was sufficient for the tested environment but should not be treated as a production sizing recommendation.

---

## 2. Victim Web Server

The monitored workload was deployed as an AWS EC2 instance running Ubuntu 24.04 LTS.

Laboratory resources:

```text
CPU:      2 vCPU
Memory:   2 GiB
Storage:  30 GiB gp3
OS:       Ubuntu 24.04 LTS
```

The workload contains:

* Nginx
* SSH
* Wazuh Agent

The Wazuh Agent was configured to communicate with the Wazuh Manager and forward security events.

---

## 3. Wazuh Agent

The Wazuh Agent was successfully registered with the Wazuh Manager.

The monitored web server appeared as an active agent and transmitted security events to the Wazuh platform.

The agent was configured to collect relevant host and application logs, including SSH and Nginx activity.

---

## 4. AWS CloudTrail Integration

CloudTrail was configured to provide AWS account and API activity logs.

The CloudTrail integration uses an S3 bucket as the log source.

The required S3 permissions include:

```text
s3:ListBucket
s3:GetObject
```

The Wazuh AWS integration periodically retrieves CloudTrail events from the configured S3 location.

The integration was configured without storing AWS access keys directly inside the Wazuh configuration.

---

## 5. Cloud Monitoring

AWS CloudWatch was also included as a monitoring source.

The project therefore combines:

```text
Host Security Logs
        +
AWS CloudTrail
        +
AWS CloudWatch
        ↓
Wazuh Security Monitoring
```

---

## 6. Network and Security Considerations

The environment was deployed as a controlled laboratory environment.

The attack machine and monitored workload were intentionally separated so that attack traffic could be generated and observed without affecting unrelated systems.

Only authorized laboratory systems were used for attack simulation.

---

## 7. Validation

After deployment, the following checks were performed:

* Wazuh Manager operational
* Wazuh Agent successfully connected
* Agent status reported as active
* Security logs received by Wazuh
* CloudTrail events available for monitoring
* Custom rules loaded successfully
* Simulated attacks generated corresponding alerts
* Dashboard displayed security events

The completed deployment provided the foundation for attack simulation and detection-rule validation.
