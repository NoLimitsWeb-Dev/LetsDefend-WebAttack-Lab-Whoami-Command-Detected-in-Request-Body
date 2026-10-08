# LetsDefend-WebAttack-Lab-Whoami-Command-Detected-in-Request-Body

## How to Detect and prevent different types of Web Attacks: SQL Injection,  Cross Site Scripting,  Command Injection,  IDOR,  RFI & LFI and File Upload (Web Shell)

## Practice with SOC Alerts
---
### 🔗118 - SOC168 - Whoami Command Detected in Request Body
---

Below is the Ticket in the Investigation Channel
<img width="1909" height="568" alt="image" src="https://github.com/user-attachments/assets/97a0f156-45bb-4f7f-9694-f08206cd6774" />

---

Click Details to view more information about the ticket
<img width="1900" height="883" alt="image" src="https://github.com/user-attachments/assets/db85796d-1c14-46be-a0d5-042e1956fa68" />

---

Click >> **Create Ticket** button to create a ticket for EventID: 118

<img width="1906" height="581" alt="image" src="https://github.com/user-attachments/assets/c1e1b126-37ef-437f-9947-54d3a55a5bcb" />

---
Click Continue to create a ticket and open the official playbook on the LetsDefend platform.
<img width="794" height="589" alt="image" src="https://github.com/user-attachments/assets/1b80d383-c634-413e-9e2d-08f64a70aebc" />

---

Click **OK**,  Once the Window showing **The ticket has been created successfully.**
<img width="831" height="609" alt="image" src="https://github.com/user-attachments/assets/b5b41960-4d82-47bc-ae08-651ceaa34469" />

---

Let's Click the blue **Start Playbook!** button to begin working through the specific investigative steps for this incident.
<img width="1911" height="623" alt="image" src="https://github.com/user-attachments/assets/83d4299d-6841-4b83-b99b-9b9e80b6ffa4" />

---

<img width="999" height="564" alt="image" src="https://github.com/user-attachments/assets/7a667275-355d-4f22-84d8-a9eec71ecb0f" />

The alert triggered because the security system detected unauthorized operating system commands ```whoami``` embedded inside the payload of an incoming HTTP request.

Specifically, the rule SOC168 - Whoami Command Detected in Request Body was tripped because an external attacker attempted a Command Injection Attack. Instead of sending normal web data, they included the Linux administrative command whoami (along with others like ls and uname) inside an HTTP POST request parameter, attempting to trick the web server into executing code directly on its underlying operating system.

---

<img width="992" height="702" alt="image" src="https://github.com/user-attachments/assets/b34ab105-4ea3-449c-9403-c0efac1e5da9" />

<img width="1914" height="949" alt="image" src="https://github.com/user-attachments/assets/ae9e8c65-9130-46c4-9f83-10387dfefd05" />


## Threat Intelligence & Traffic Analysis Overview

 ### VirusTotal

|  Analysis Target  |  Metric / Value  |  Investigative Source  |
|  :---  |  :---  |  :---  |
|  Attacker IP Address  |  61.177.172.87  |	Logs & Threat Intel  |
|  IP Reputation  |	Malicious / Suspicious (Flagged by security vendors)	|  VirusTotal IP Report  |
|  ISP / Autonomous System	|  AS4134 (Chinanet)	|  VirusTotal Details  |
|  Traffic Direction	|  Internet to Local (External to Internal)	|  Network Log Triangulation  |


Key Findings from Evidence

• Malicious Intent Verified: The VirusTotal scan shows that security vendors (including ADMINUSLabs and Fortinet) have explicitly flagged this IP address for Malicious and Malware activities.

• Inbound Attack Vectors: The traffic originated completely outside the company network pool, confirming this was an inbound malicious attempt targeting an internal server infrastructure asset.

---
<img width="1857" height="907" alt="image" src="https://github.com/user-attachments/assets/ad13fe0b-ba9e-4530-bab8-38b38b70aa73" />
<img width="1912" height="837" alt="image" src="https://github.com/user-attachments/assets/c654bffa-9393-4c21-8b55-52d9a8486042" />



### AbuseIPDB 

Reputation Report

• Total Abuse Reports: 85,755 times from 528 distinct sources.
• ISP & Network: CHINANET jiangsu province network (AS4134).
• Location: Nanjing, Jiangsu, China.
• Activity Status: Actively Abusive. Recent reports confirm ongoing malicious activity (such as web application attacks and SSH brute-forcing).
Combined with above VirusTotal findings, this overwhelming number of historical abuse reports completely solidifies the Malicious act of the IP.

---

<img width="1916" height="724" alt="image" src="https://github.com/user-attachments/assets/0a824d13-234d-4977-94e3-d848d9984ce1" />

<img width="1909" height="900" alt="image" src="https://github.com/user-attachments/assets/2e4b6bee-d235-4304-a77b-7debc499e173" />


### Critical Evidence Breakdowns

|  Evidence Type	|  Verified Detail / Value	|  Investigative Proof |
|  :---  |  :---  |  :---  |
|  HTTP Response Status	|  HTTP 200 (OK)	|  The web server processed the payload and replied normally instead of blocking it with a 403 Forbidden or 500 Error. |
| Injected Commands	| ls, whoami, uname -a | Visible inside the raw HTTP POST request body under the c parameter. |
| Command Response Data	| whoami -> rootuname -a -> Linux WebServer1004... | The response body directly contains the evaluation outputs of the server's root terminal context. |


### Additional Critical Takeaways from This Log

The presence of cat /etc/passwd (and its companion payload cat /etc/shadow) in the logs signifies that the attacker was attempting Targeted Local Data Exfiltration and Privilege Escalation.

• Accessing Password Hashes: The /etc/shadow file stores the actual encrypted password hashes for all system users on a Linux machine.
* Proof of Root Execution: Under normal security configurations, only the root (administrator) user can read this file. Because the request received an HTTP Response Status: 200 with a substantial response size (1501 bytes), it heavily indicates that the web server process is running with root privileges and successfully dumped the password hashes directly to the attacker.
• Device Action Failure: The line Device Action: Permitted shows that your perimeter security controls (like a WAF or IPS) completely failed to detect or block this highly signature-based attack payload, allowing it straight through to the vulnerable backend application.


### The Security Implications of These Commands

• cat /etc/passwd: This command is used to read the system's password file. While modern Linux distributions don't store actual secret passwords in this specific file anymore (they store them as hashes in /etc/shadow), reading /etc/passwd dumps a complete map of every user account, system service profile, home directory path, and active shell configured on that web server. Attackers use this to identify target usernames to target for lateral movement or subsequent login attempts.
• cat /etc/shadow: This is the much more dangerous companion attempt. The /etc/shadow file contains the actual encrypted and hashed system passwords. Only the root user is authorized to read it.

### Why This Confirms Total Server Compromise

In your log management context, seeing that the server successfully printed the output of these commands back to the attacker confirms two critical findings:
1. System Reconnaissance: The attacker didn't just test if command injection worked (using whoami); they actively began harvesting internal data to fully take over the server.
2. Root Privilege Confirmation: Because the web application successfully read out these system files, it proves that your underlying web service was running with high-level administrative system permissions (root privileges). This misconfiguration allowed the attacker complete command execution access.

---

<img width="1006" height="423" alt="image" src="https://github.com/user-attachments/assets/c97e20e4-31e4-40da-9463-8d4c6679a01a" />

Based on our clear findings—the VirusTotal and AbuseIPDB reputation reports, alongside the concrete evidence of OS commands like whoami and cat /etc/shadow being passed in the POST parameter payload—this traffic represents a highly dangerous web application exploit.

---

<img width="1005" height="422" alt="image" src="https://github.com/user-attachments/assets/1941e269-4b7a-419a-9112-1901df38e84a" />

• The Evidence: In this case, the web application directly executed operating system terminal commands passed into the web parameter, such as whoami, uname, and cat /etc/shadow, returning the backend server's terminal outputs right back to the attacker.

---

<img width="987" height="588" alt="image" src="https://github.com/user-attachments/assets/38b46e50-f349-4ae9-a82d-238f50d5765a" />

• Malicious External Source: The attack originates from an external IP (61.177.172.87) with an extensively documented history of hostile web exploits, rather than an authorized internal simulation platform or designated corporate penetration testing subnet.

---
• No Internal Authorization: There are no internal mailbox alerts or authorized change tickets scheduled for this time frame on the LetsDefend platform.

<img width="1504" height="689" alt="image" src="https://github.com/user-attachments/assets/61988fde-604a-4938-89d8-c496f5782332" />

---

<img width="1000" height="436" alt="image" src="https://github.com/user-attachments/assets/4436d136-c4a5-4fa0-b653-43dd8a4ff193" />

• Source: The attack originates from an external, public IP address (61.177.172.87) hosted on the Internet.
• Destination: The traffic targets an internal private IP address (172.16.17.16) belonging to your Company Network.

---

<img width="974" height="780" alt="image" src="https://github.com/user-attachments/assets/f3016f7e-97ee-4866-b22d-0d6b95f44481" />
<img width="1866" height="882" alt="image" src="https://github.com/user-attachments/assets/545d6224-afc0-4cd5-bb31-7de4d28049e4" />

The image above shows the Terminal History logs for WebServer1004 (IP: 172.16.17.16) inside the Endpoint Security panel.
The image provides absolute, definitive proof that the operating system shell executed the injected payloads. The listed command-line entries match the exact timestamps and request patterns found in your malicious network traffic logs.

### Analysis of the Terminal Logs

• System Discovery: The commands ls, whoami, and uname were directly run by the system on 28.02.2022 between 04:11 and 04:13.

• Data Exfiltration: The highly sensitive system files cat /etc/passwd and cat /etc/shadow were successfully run in the shell right after at 04:14 and 04:17.

• Verdict: Because these commands are logged as having executed inside the machine's backend command history, the asset is fully compromised.

• HTTP 200 OK Response: The web server processed the commands and successfully returned the text output of these sensitive administrative files back to the external attacker.

<img width="994" height="362" alt="image" src="https://github.com/user-attachments/assets/36458d52-6667-40ca-8730-a692f5df2214" />

---

<img width="982" height="684" alt="image" src="https://github.com/user-attachments/assets/fe8be981-fac9-43e4-b772-58a12a8f969a" />
<img width="1894" height="843" alt="image" src="https://github.com/user-attachments/assets/1beb6d76-83b4-45a6-b3ca-a5682c00db63" />

* **Confirm Containment:** Select Yes or check the box indicating that the host has been successfully isolated.

---

<img width="986" height="667" alt="image" src="https://github.com/user-attachments/assets/ee620e6c-3bab-45b3-a2c0-8ae3f01f1e32" />

Select Yes on your screen.

Why This Is Correct

• The Attack Succeeded: The playbook guidelines explicitly state to escalate "...in cases where the attack succeeds."

• Proven Compromise: Our earlier investigation of the terminal history and HTTP 200 logs conclusively proved that the external attacker successfully ran administrative commands (whoami, cat /etc/shadow) as root on WebServer1004.

---

### 1. Artifact 1:
	
 • Value: 61.177.172.87
	
 • Type: IP
	
 • Comment: Attacker source IP address executing command injection payloads.

### 2. Artifact 2:
	
 • Value: /video/?c=whoami
	
 • Type: URL
	
 • Comment: Vulnerable web application endpoint targeted by the attacker.

<img width="982" height="566" alt="image" src="https://github.com/user-attachments/assets/8c4c175b-2709-471b-ba50-88b8370edee5" />

---

<img width="1001" height="588" alt="image" src="https://github.com/user-attachments/assets/a26ac062-1eb6-4728-95f2-a0c6197e69f7" />

```
Incident Summary:
The SOC team detected a successful Command Injection attack targeting WebServer1004 (172.16.17.16) at the path /video/. The attack originated from an external public IP address (61.177.172.87), which threat intelligence resources (VirusTotal and AbuseIPDB) confirm has a highly malicious reputation and a history of active web exploits.
Investigation & Impact:
Analysis of the incoming HTTP POST request logs revealed that the attacker appended unauthorized system commands (whoami, uname, ls, cat /etc/passwd, and cat /etc/shadow) inside the request parameters. Examination of the endpoint’s Terminal History confirmed that the target server successfully executed these commands in a high-privilege system context (root) and returned an HTTP 200 response containing the output of sensitive files back to the threat actor.
Mitigation & Action Taken:
• Flagged the incident as a True Positive.
• Successfully isolated WebServer1004 via Endpoint Security control panels to prevent further lateral movement or additional data exfiltration.
• Logged relevant Indicators of Compromise (IoCs).
• Escalated the ticket to Tier 2 for full system remediation, forensic log cleanup, and mandatory credential rotations.
```

---

<img width="1002" height="462" alt="image" src="https://github.com/user-attachments/assets/f6895016-1899-40f9-88c9-64a895500b1f" />

* Select True Positive on your screen

* Click the blue **Confirm & Close** button to submit your final results.

---

### Core Verdict Reasons

• Malicious Commands: The external attacker deliberately embedded dangerous operating system shell commands (whoami, cat /etc/shadow) inside the web request body parameters.

• Successful Compromise: The web server completely executed the code payloads under root administrative privileges and exposed sensitive password hashes back to the internet.

---

<img width="1903" height="881" alt="image" src="https://github.com/user-attachments/assets/e27b5880-8243-4b76-bb35-d413a24084de" />

---
