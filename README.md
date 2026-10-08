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

|  Evidence Type	|  Verified Detail / Value	|  Investigative Proof  |
|  HTTP Response Status	|  HTTP 200 (OK)	|  The web server processed the payload and replied normally instead of blocking it with a 403 Forbidden or 500 Error.  \
Injected Commands	ls, whoami, uname -a	Visible inside the raw HTTP POST request body under the c parameter.
Command Response Data	whoami -> root
uname -a -> Linux WebServer1004...	The response body directly contains the evaluation outputs of the server's root terminal context.
