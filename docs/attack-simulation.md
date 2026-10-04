# Attack Simulation

## Objective

Controlled attack simulations were performed to validate the ability of Wazuh to detect multiple stages of an attack against the monitored workload.

The simulation followed a simplified attack chain:

```text
Reconnaissance
      ↓
Initial Access
      ↓
Successful Authentication
      ↓
Privilege Escalation
      ↓
Attack Chain Correlation
```

All activities were performed against authorized laboratory systems.

---

## 1. Reconnaissance

Nmap was used to identify exposed services and perform service discovery against the victim workload.

Example:

```bash
nmap -Pn -sS -sV <TARGET_IP>
```

The generated network activity was monitored and analyzed by the Wazuh environment.

The purpose of this stage was to validate detection of reconnaissance and scanning behavior.

---

## 2. SSH Brute Force

Hydra was used to generate repeated SSH authentication attempts against the monitored server.

The simulation generated multiple failed authentication events.

These events were collected by the Wazuh Agent and analyzed by the custom detection rules.

The objective was to detect:

* Repeated SSH authentication failures
* Brute-force behavior
* Abnormal authentication frequency

---

## 3. Successful SSH Authentication

After the brute-force activity, a successful SSH authentication event was generated.

The successful authentication was correlated with the previous failed attempts.

This allowed the monitoring system to distinguish a potential successful compromise from isolated authentication failures.

---

## 4. Privilege Escalation

A controlled privilege escalation scenario was performed using `sudo`.

The resulting activity generated security events that were collected by Wazuh.

A dedicated detection rule was used to identify the privilege escalation event.

---

## 5. Attack Chain Correlation

The individual events were combined to identify the complete attack sequence.

```text
Nmap Reconnaissance
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
Full Attack Chain
```

The correlation rule provides a higher-severity alert when multiple stages of the simulated attack occur within the defined correlation window.

---

## 6. Detection Validation

Each attack stage was mapped to an expected detection event.

| Attack Stage          | Expected Detection                       |
| --------------------- | ---------------------------------------- |
| Nmap reconnaissance   | Port scanning activity                   |
| SSH brute force       | Multiple failed SSH logins               |
| Successful SSH access | Successful authentication after failures |
| Privilege escalation  | Suspicious sudo activity                 |
| Complete attack chain | Correlated multi-stage attack            |

The simulations successfully generated the expected security events used for rule validation.

---

## 7. Purple Team Approach

The attack simulations were not performed only to demonstrate offensive techniques.

The results were fed back into the defensive detection process.

```text
Attack Simulation
       ↓
Security Event
       ↓
Detection Rule
       ↓
Alert
       ↓
Investigation
       ↓
Rule Tuning
       ↓
Improved Detection
```

This iterative process represents the Purple Team aspect of the project.
