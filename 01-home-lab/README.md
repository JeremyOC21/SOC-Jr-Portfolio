# SOC Home Lab

## 1. Objective

Document the architecture, network segmentation, and resource allocation
of a self-built SOC home lab used to practice security monitoring, alert
triage, and incident response.

## 2. Environment

- **Virtualization:** PNETLab over VMware Workstation
- **Host hardware:** Ryzen 5 4500, 16 GB RAM, GTX 1660 Super
- **Guest systems:** Ubuntu Server 22.04, Windows 10 Pro, Kali Linux,
  pfSense 2.7, Metasploitable 2

## 3. Network architecture

The lab is segmented into isolated networks, all routed and filtered by a
central pfSense firewall. This mirrors how a real organization separates
user, server, and management zones.

```
                       [ Internet (NAT) ]
                               |
                          [ pfSense ]
         ______________________|______________________
        |            |              |                 |
    LAN 10.10.10   DMZ 10.10.20   SOC 10.10.30    ATK 10.10.99
    Win10          Ubuntu-Web     Wazuh           Kali
    WinServer      Metasploitable
```

### Network segments

| Segment | Subnet | Purpose | Hosts |
|---|---|---|---|
| LAN | 10.10.10.0/24 | User zone | Windows 10, Windows Server |
| DMZ | 10.10.20.0/24 | Public-facing services | Ubuntu web, Metasploitable |
| SOC | 10.10.30.0/24 | Monitoring zone | Wazuh |
| ATK | 10.10.99.0/24 | Attacker (isolated) | Kali Linux |
| MGMT | Cloud0 | Out-of-band management | Wazuh dashboard access |

### Key design decisions

- **The attacker (ATK) is blocked from reaching the SOC segment** by a
  firewall rule, mirroring real environments where an attacker who
  compromises a host should not be able to reach the monitoring system.
- **Wazuh has two interfaces:** one on the SOC network (to receive agent
  logs) and one on the management network (for the analyst to reach the
  dashboard). This separates production traffic from administrative
  access.
- **The DMZ hosts deliberately vulnerable systems** (Metasploitable, an
  outdated Ubuntu) to safely practice detection of attacks against
  exposed services.

## 4. Resource allocation

Because the host has 16 GB RAM, machines are started per scenario rather
than all at once. A typical monitoring scenario runs pfSense + Wazuh +
one or two endpoints.

| Machine | Role | RAM | vCPU |
|---|---|---|---|
| pfSense | Firewall / router | 1 GB | 1 |
| Wazuh | SIEM | 4–5 GB | 2–4 |
| Windows 10 | Monitored endpoint | 2 GB | 2 |
| Windows Server 2019 | Domain controller (optional) | 2 GB | 2 |
| Kali Linux | Attacker | 1.5 GB | 1 |
| Metasploitable 2 | Vulnerable target | 512 MB | 1 |

## 5. What this lab enables

- Collecting Windows and Sysmon telemetry into a SIEM
- Generating controlled attacks from an isolated attacker host
- Practicing alert triage, investigation, and incident documentation
- Testing firewall rules and network segmentation
- Vulnerability and configuration assessment of endpoints

## 6. Evidence

![Lab topology in PNETLab](../evidence/00-lab-topology.png)

## 7. Lessons Learned

- Network segmentation is not just theory: the ATK→SOC block rule is the
  same principle used to protect monitoring infrastructure in production.
- Resource constraints force good habits — starting only the machines a
  scenario needs, and shutting them down cleanly to avoid disk corruption.

## 8. References

- Wazuh documentation — https://documentation.wazuh.com/
- pfSense documentation — https://docs.netgate.com/pfsense/
