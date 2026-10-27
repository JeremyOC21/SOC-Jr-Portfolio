# SOC Jr Portfolio | Cybersecurity Lab

## About me

Aspiring Junior SOC Analyst based in Costa Rica, with a background in
Systems Engineering coursework (cybersecurity, digital forensics, and
ethical hacking) and a hands-on security home lab.

I build practical skills in security monitoring, alert triage, Windows
event analysis, firewall log investigation, and incident documentation.
My goal is to start my career as a SOC Level 1 Analyst.

## Technical environment

- **SIEM:** Wazuh 4.14 (manager, indexer, dashboard on Ubuntu Server 22.04)
- **Endpoint telemetry:** Windows 10 with Sysmon (SwiftOnSecurity config)
- **Firewall:** pfSense 2.7 (segmented network: LAN, DMZ, SOC, ATK)
- **Attacker host:** Kali Linux
- **Virtual lab:** PNETLab over VMware
- **Networking:** TCP/IP, DNS, DHCP, firewall rules, network segmentation
- **Operating systems:** Windows and Linux

## Projects

### 01. SOC Home Lab
**Status: Completed**

Network topology, virtual machines, IP addressing, and resource
allocation of a segmented SOC lab built in PNETLab.

[View project](./01-home-lab/README.md)

### 02. Wazuh and Windows Monitoring
**Status: Completed**

Deploying the Wazuh agent and Sysmon on Windows 10, validating log
collection, and investigating alerts to separate benign activity from
real threats (including false-positive analysis via process pivoting).

[View project](./02-wazuh-windows-monitoring/README.md)

### 03. Firewall and Network Analysis
**Status: Planned**

Reviewing pfSense firewall rules and logs, and correlating network
traffic (IPs, ports, protocols) with security events.

[View project](./03-firewall-network-analysis/README.md)

### 04. Incident Investigation — RDP Brute Force
**Status: Completed**

Full detection-to-defense cycle: detecting an RDP brute-force attack in
Wazuh, investigating it, identifying the root cause in the system's
configuration (CIS SCA), hardening the host, and verifying the fix by
re-running the attack.

[View project](./04-incident-response/README.md)

### 05. SOC Playbooks
**Status: Completed**

Repeatable procedures for alert triage, evidence collection,
escalation, and reporting.

[View project](./05-soc-playbooks/README.md)

## Skills in development

- Security event analysis (Windows Event Log, Sysmon)
- Alert triage and prioritization
- False-positive assessment and process pivoting
- Basic incident response (detect, investigate, contain, verify)
- Vulnerability and configuration assessment (CVE, CIS benchmarks)
- Network traffic and firewall log analysis
- MITRE ATT&CK mapping
- Technical documentation

## Evidence and methodology

Each project includes its objective, environment details, sanitized
screenshots, observations, and conclusions. Only validated results are
presented as completed work. All practical exercises are performed in an
authorized, isolated lab environment.

## Contact

- **LinkedIn:** https://www.linkedin.com/in/jeremy-obando-caceres-809a6b303/
- **Email:** ojeremy763@gmail.com
- **Location:** Costa Rica
- **English:** B1 (self-assessed)
