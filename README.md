# AD-Splunk-Lab
```mermaid
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
Client1: Domain-joined Workstation  ip static set as: 192.168.10.20
Client2: Domain-joined Workstation  ip static set as: 192.168.10.30
Client3: Domain-joined Workstation  ip static set as: 192.168.10.40
Client4: Domain-joined Workstation  ip static set as: 192.168.10.50
Client5: Domain-joined Workstation  ip static set as: 192.168.10.60


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


**Challenges and Troubleshooting**
My only two major and time consuming issues were getting the Windows 11 Eval fully operational, and getting the domain controller to communicate with the client.\

For the Windows 11 Eval, booting it in the Virtualbox VM would not boot the OS, I figured it was not a boot error because it would allow me to change the UEFI settings, my thought process was that the VM was already dedicating 80GB of disk space, which I had made different from the usual 50 thinking it would be enough, sure enough it was not. After allocating 100GB to the VM, The OS booted normally. After underestimating the total size needed to run my current 5 client workstation VMs, I purchased additional external hard drives be able to handle the payload for the future.

The Windows 11 client was unable to join the lab.local domain and could not reach the Domain Controller, despite being on the same virtual network. and after plenty of trial and error, I discovered that the Windows Defender Firewall was not allowing traffic to be received on port 9997. I fixed this by enabling the "Receiving" port (9997) within the Splunk Enterprise settings and then configured an inbound firewall rule on the Windows Server to allow traffic over TCP port 9997.


**Future Goals**
Add Suricata IDS for network-based intrusion detection
Simulate additional attacks (pass-the-hash, Kerberoasting)
Integrate AI-powered log analysis using Python, LangChain, VirusTotal and Gemini
Fully Operational Ticketing System

graph TD
    subgraph "VirtualBox Internal Network (192.168.10.0/24)"
        DC01["<b>DC01: Domain Controller</b><br/>Windows Server 2022<br/>Splunk Enterprise (Indexer)<br/>192.168.10.10"]
        
        C1["<b>Client 1</b><br/>Chug Biggums<br/>192.168.10.20"]
        C2["<b>Client 2</b><br/>Chester McBoof<br/>192.168.10.30"]
        C3["<b>Client 3</b><br/>Fargle Von Bargle<br/>192.168.10.40"]
        C4["<b>Client 4</b><br/>Jake Trembler<br/>192.168.10.50"]
        C5["<b>Client 5</b><br/>Jean Shotblock<br/>192.168.10.60"]
    end

    %% Log Forwarding Traffic
    C1 -- "UF: Port 9997" --> DC01
    C2 -- "UF: Port 9997" --> DC01
    C3 -- "UF: Port 9997" --> DC01
    C4 -- "UF: Port 9997" --> DC01
    C5 -- "UF: Port 9997" --> DC01

    %% Analysis flow
    DC01 -- "Real-time Analysis" --> Dash[("SOC Security Dashboard")]

    %% Styling for better visibility
    style DC01 fill:#f9f,stroke:#333,stroke-width:2px
    style Dash fill:#9f9,stroke:#333,stroke-width:2px
```
