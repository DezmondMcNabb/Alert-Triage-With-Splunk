[TryHackMe – Alert Triage With Splunk.md](https://github.com/user-attachments/files/31869014/TryHackMe.Alert.Triage.With.Splunk.md)
# Alert-Triage-With-Splunk
TryHackMe – Alert Triage With Splunk
# TryHackMe – Alert Triage With Splunk

## Overview

This project demonstrates a hands-on Security Operations Center (SOC) investigation using Splunk to triage and analyze security alerts across Linux, Windows, and web server environments.

The lab focused on investigating suspicious activity by analyzing security logs, identifying indicators of compromise, correlating events, and determining the actions performed by a threat actor.

Three primary security incidents were investigated:

- SSH brute-force and initial access activity
- Windows scheduled task persistence
- Web shell activity on a compromised web server

The investigation simulated the responsibilities of a SOC analyst responding to security alerts and using SIEM data to reconstruct attacker activity.

---

## Objectives

The primary objectives of this lab were to:

- Investigate security alerts using Splunk.
- Analyze authentication and system logs for malicious activity.
- Identify successful access following brute-force attempts.
- Investigate privilege escalation and persistence.
- Analyze Windows process and security events.
- Detect suspicious scheduled task activity.
- Investigate web shell activity.
- Correlate events to reconstruct attacker behavior.
- Determine whether alerts represented legitimate malicious activity.

---

## Tools & Technologies

- **Splunk**
- **Splunk Search Processing Language (SPL)**
- **TryHackMe**
- **Linux Authentication Logs**
- **Windows Security Logs**
- **Windows Process Creation Events**
- **Web Server Access Logs**
- **SSH**
- **Windows Task Scheduler**
- **Hydra**
- **Web Shells**

---

# Investigation 1 – SSH Brute-Force & Initial Access

## Alert Overview

The first investigation involved suspicious authentication activity against a Linux system.

The objective was to determine whether the authentication failures represented a brute-force attack and identify what actions occurred after the attacker obtained access.

## Authentication Analysis

Linux authentication logs were analyzed in Splunk to identify activity associated with the targeted account.

The investigation revealed approximately **500 failed SSH authentication attempts** against the user:

`john.smith`

The high number of authentication failures against a single account strongly indicated automated password guessing or brute-force activity.

Further analysis of the timestamps established the duration of the attack and helped construct a timeline of the authentication activity.

<img width="2774" height="916" alt="image" src="https://github.com/user-attachments/assets/e364d83f-db50-4573-aad6-90ee27b36b97" />


---

## Successful Access & Post-Compromise Activity

The investigation continued by searching for successful authentication events following the failed login attempts.

A successful SSH login was identified after the brute-force activity, indicating that the attacker was eventually able to obtain valid credentials and access the Linux system.

Analysis of subsequent events revealed additional post-compromise activity, including:

- Privilege escalation
- Creation of an additional user account
- Establishment of persistence on the compromised system

The creation of another account could allow the attacker to maintain access even if the originally compromised credentials were disabled or reset.

<img width="2800" height="1106" alt="image" src="https://github.com/user-attachments/assets/1755b7a0-7507-4e70-8b35-e0de757ff4f7" />


## Initial Access Findings

The investigation established the following attack sequence:

**SSH Brute Force → Successful Authentication → Privilege Escalation → Account Creation → Persistence**

The combination of repeated authentication failures, successful access, and subsequent system changes confirmed that the alert represented a **True Positive** security incident.

---

# Investigation 2 – Windows Scheduled Task Persistence

## Alert Overview

The second investigation involved suspicious activity on a Windows endpoint.

An alert indicated that a potentially malicious scheduled task had been created.

Scheduled tasks are commonly used for legitimate administrative purposes, but attackers can also abuse them to execute malicious programs automatically and maintain access to compromised systems.

## Scheduled Task & Process Analysis

Windows logs were analyzed in Splunk to locate events associated with the suspicious scheduled task.

The investigation identified the process responsible for creating the task and its associated **Process ID (PID)**.

Process creation events were then analyzed to understand the parent-child process relationship surrounding the suspicious activity.

Examining these relationships provided additional context about how the scheduled task was created and helped reconstruct the attacker's execution chain.


---

## Discovery Activity

Additional Windows events showed that the attacker performed local system discovery.

The attacker enumerated a local Windows group, which could be used to identify accounts with elevated permissions and determine potential targets for privilege escalation or lateral movement.

Authentication and Windows event data were also correlated to identify the workstation associated with the malicious session.

<img width="2800" height="1146" alt="image" src="https://github.com/user-attachments/assets/d3a04205-c017-40c3-87bc-f745041e792d" />


## Persistence Investigation Findings

The Windows investigation established the following general attack sequence:

**System Access → Process Execution → Scheduled Task Creation → Persistence → Account/Group Discovery**

The scheduled task activity, process relationships, and discovery events provided strong evidence that the alert represented malicious activity.

The alert was classified as a **True Positive**.

---

# Investigation 3 – Web Shell Activity

## Alert Overview

The final investigation involved a potentially compromised web server.

An alert indicated that a possible web shell had been uploaded to the server.

A web shell is a malicious server-side script that allows an attacker to remotely execute commands or interact with a compromised web server.

## Brute-Force & Web Shell Investigation

Web server logs were analyzed in Splunk to identify suspicious activity surrounding the alert.

The investigation revealed brute-force activity associated with **Hydra**, an automated password-guessing tool.

Analysis of the timestamps helped determine when the attack began and provided the starting point for the incident timeline.

After obtaining access, the attacker deployed and interacted with a web shell on the compromised server.

Splunk was used to isolate requests associated with the malicious resource and analyze the attacker's interaction with it.

---

## HTTP Traffic Analysis

HTTP request data associated with the web shell was further analyzed.

The investigation identified the **User-Agent** associated with the attacker and determined the number of requests made through the malicious web shell.

User-Agent information provided another indicator that could be used to isolate related attacker activity within the web server logs.

The presence of repeated requests to the web shell demonstrated that the malicious file was actively being used rather than simply uploaded to the server.

<img width="1952" height="668" alt="image" src="https://github.com/user-attachments/assets/1ef17f0f-7749-445d-82c1-830e47062446" />


## Web Shell Investigation Findings

The investigation reconstructed the following attack sequence:

**Brute-Force Activity → Successful Access → Web Shell Deployment → Web Shell Execution → Remote Attacker Interaction**

Based on the evidence collected from the web server logs, the alert was determined to be a **True Positive**.

---

# Overall Findings

Across all three investigations, Splunk was used to move beyond the initial security alerts and determine what actually occurred on the affected systems.

The investigations identified several examples of malicious behavior, including:

- SSH brute-force attacks
- Successful unauthorized authentication
- Privilege escalation
- Persistence through account creation
- Windows scheduled task persistence
- Suspicious process relationships
- Local group enumeration
- Web application brute-force activity
- Web shell deployment
- Malicious HTTP requests

Each investigation required correlating multiple events rather than relying solely on the original security alert.

---

# Attack Flow

The overall attacker behavior observed throughout the lab can be summarized as:

**Initial Access**

Brute Force  
↓  
Successful Authentication

**Post-Compromise Activity**

Privilege Escalation  
↓  
Discovery

**Persistence**

Account Creation / Scheduled Task  
↓  
Continued System Access

**Web Compromise**

Web Authentication Attack  
↓  
Web Shell Deployment  
↓  
Remote Interaction

---

# Key Takeaways

This lab demonstrated the importance of investigating the context surrounding security alerts.

An alert should serve as the starting point of an investigation rather than the final conclusion.

Splunk made it possible to search large volumes of log data, correlate activity, identify attacker behavior, and reconstruct attack timelines across Linux, Windows, and web environments.

The investigation also reinforced the importance of recognizing abnormal authentication patterns, suspicious process relationships, persistence mechanisms, and unusual web traffic when determining whether an alert represents a True Positive or False Positive.

---

# Skills Demonstrated

- SIEM Alert Triage
- Splunk
- SPL Querying
- Security Log Analysis
- Incident Investigation
- Threat Detection
- SSH Brute-Force Analysis
- Windows Event Analysis
- Linux Log Analysis
- Web Server Log Analysis
- Authentication Analysis
- Process Analysis
- Persistence Detection
- Privilege Escalation Analysis
- Web Shell Detection
- IOC Identification
- Attack Timeline Reconstruction
- True Positive / False Positive Determination

---

## Conclusion

The TryHackMe **Alert Triage With Splunk** lab provided hands-on experience investigating realistic SOC alerts using Splunk.

Through three investigation scenarios, I analyzed Linux, Windows, and web server logs to identify brute-force attacks, compromised accounts, privilege escalation, persistence mechanisms, discovery activity, and web shell interactions.

The lab strengthened my ability to use Splunk as an investigative SIEM platform and demonstrated how log correlation can be used to move from an initial alert to an evidence-based understanding of a security incident.
