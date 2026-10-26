# Firewall and Network Analysis

## Status: Planned

## 1. Objective

Review pfSense firewall rules and logs, and correlate network traffic
(source/destination IPs, ports, protocols) with security events, to build
network-level visibility on top of endpoint detection.

## 2. Environment

- **Firewall:** pfSense 2.7 (segmented: LAN, DMZ, SOC, ATK)
- **SIEM:** Wazuh 4.14
- **Virtualization:** PNETLab over VMware

## 3. Planned scope

- Document the firewall rule set per interface and what each rule enforces
  (e.g., the ATK→SOC block, per-segment outbound rules).
- Review the pfSense firewall logs (allowed vs. blocked traffic).
- Forward pfSense logs to Wazuh so network events appear alongside
  endpoint alerts.
- Correlate a network scan or blocked connection with its firewall log
  entry, identifying the IPs, ports, and protocols involved.

## 4. Why this matters

Endpoint telemetry shows what happens *on* a host, but the firewall sees
what crosses the *network*. A port scan, for example, is often barely
visible on the target endpoint but fully visible at the firewall. Adding
firewall visibility lets an analyst see an attack as it enters the
network, not only when it reaches a victim.

## 5. Status note

This project is planned. The lab topology and firewall rules are already
built (see project 01); forwarding pfSense logs to Wazuh and documenting
the traffic analysis is the next step.

## 6. References

- pfSense documentation — https://docs.netgate.com/pfsense/
- Wazuh — monitoring pfSense — https://documentation.wazuh.com/
