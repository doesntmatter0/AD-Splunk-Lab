# AD-Splunk-Lab
Built a fully functional Active Directory home lab enviornment in order to simulate a corporate SOC network and gain skills for a SOC enviornment. I used Splunk Enterprise to simulate a SIEM. This lab uses Windows Server 22 as a domain controller, multiple workstations joined from a windows 11 evaluation domain, and a Splunk Enterprise instanace collecting and analysing data and security events in real time.


**Tools & Technologies**

VirtualBox — Hypervisor for running virtual machines
Windows Server 2022 — Domain Controller running Active Directory Domain Services
Windows 11 Enterprise — Domain-joined client workstation
Splunk Enterprise — SIEM for log collection and analysis
Splunk Universal Forwarder — Log shipping agent installed on both VMs
Active Directory Domain Services (AD DS) — User and computer management
DNS — Internal domain name resolution for lab.local


**Network Architecture**

DC01: Domain Controller / Splunk Server with ip static set as: 192.168.10.10
Client1: Domain-joined Workstation "Chug Biggums" ip static set as: 192.168.10.20
Client2: Domain-joined Workstation "Chester McBoof" ip static set as: 192.168.10.30
Client3: Domain-joined Workstation "Fargle Von Bargle" ip static set as: 192.168.10.40
Client4: Domain-joined Workstation "Jake Trembler" ip static set as: 192.168.10.50
Client5: Domain-joined Workstation "Jean Shotblock" ip static set as: 192.168.10.60


**Splunk Queries for dashboard**

Detect failed logins:
index=main source="WinEventLog:Security" EventCode=4625 | timechart count by host
Detect successful logins:
index=main source="WinEventLog:Security" EventCode=4624 | timechart count by host
Detect account lockouts:
index=main source="WinEventLog:Security" EventCode=4740 | table _time, Account_Name, Computer
Detect brute force pattern (multiple failures then success):
index=main source="WinEventLog:Security" (EventCode=4625 OR EventCode=4624) | timechart count by EventCode



**Steps taken**

Phase 1 — Active Directory Setup

Deployed Windows Server 2022 as a domain controller for lab.local
Configured Active Directory Domain Services, DNS, and DHCP
Created Organizational Units (OUs) and test user accounts
Joined a Windows 11 workstation to the domain
Configured static IPs and internal DNS resolution between VMs

Phase 2 — Splunk SIEM Deployment

Installed Splunk Enterprise on DC01 as the central log collection server
Deployed Splunk Universal Forwarder on both DC01 and Client01
Configured inputs.conf to collect Windows Security, System, and Application event logs
Opened firewall rules to allow log forwarding on port 9997
Verified log ingestion from both machines in Splunk

Phase 3 — Security Monitoring & Attack Simulation

Simulated brute force attacks by generating multiple failed login attempts
Triggered account lockout events through repeated authentication failures
Used Splunk SPL queries to detect and investigate security events
Built a real-time security dashboard with the following panels:

Failed Login Attempts (Event ID 4625)
Successful Logins (Event ID 4624)
Account Lockouts (Event ID 4740)


**Screenshots**
<img width="2560" height="1440" alt="Screenshot (40)" src="https://github.com/user-attachments/assets/1ce9e7d7-ce90-4a28-bddd-233c70ddf100" />
<img width="2560" height="1440" alt="Screenshot (41)" src="https://github.com/user-attachments/assets/02180a30-5e2d-4daf-b2ca-46d6ad72aa69" />
<img width="2560" height="1440" alt="Screenshot (39)" src="https://github.com/user-attachments/assets/3d7e7462-241f-46c4-9664-3dbddff81ac0" />
<img width="2560" height="1440" alt="Screenshot (42)" src="https://github.com/user-attachments/assets/6cce13da-2338-4c6c-8c28-67f9d5feced4" />


**Future Goals**
Add Suricata IDS for network-based intrusion detection
Simulate additional attacks (pass-the-hash, Kerberoasting)
Integrate AI-powered log analysis using Python, LangChain, VirusTotal and Gemini
Fully Operational Ticketing System
