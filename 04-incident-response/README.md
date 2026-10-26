# Incident Investigation — RDP Brute-Force Attack

## 1. Objective

Detect, investigate, and respond to a remote brute-force attack against
the Remote Desktop Protocol (RDP) service of a Windows 10 endpoint, and
verify that a hardening measure effectively stops the attack.

This project demonstrates the full SOC workflow: **detect → investigate
→ find root cause → harden → verify.**

## 2. Environment

- **SIEM:** Wazuh 4.14 (Ubuntu Server 22.04)
- **Endpoint:** Windows 10 Pro + Sysmon + Wazuh agent (10.10.10.100)
- **Firewall:** pfSense 2.7 (segmented network)
- **Attacker:** Kali Linux (10.10.99.102)
- **Virtualization:** PNETLab over VMware
- **Network:** `LAN 10.10.10.0/24` (victim) and `ATK 10.10.99.0/24` (attacker),
  routed and filtered by pfSense

## 3. Scenario

RDP is one of the most targeted services on the internet. Identity-based
attacks (brute force, password spraying, credential stuffing) are the
leading initial-access vector observed in the current threat landscape.

In this exercise, the attacker host runs an automated password-guessing
tool (Hydra) against the victim's exposed RDP service to simulate a
real-world brute-force attempt. A SOC L1 analyst must detect it,
determine whether it succeeded, and recommend containment.

## 4. Methodology

1. Reconnaissance: scan the victim to confirm the exposed RDP service.
2. Launch an automated brute-force attack against RDP.
3. Detect the attack in the Wazuh dashboard (Threat Hunting).
4. Investigate the alerts and determine if access was gained.
5. Identify the root cause in the host configuration (CIS SCA).
6. Harden the endpoint (enable account lockout policy).
7. Re-run the attack and verify the defense works.

## 5. Evidence

| File | Description |
|---|---|
| `01-nmap-recon.png` | Port scan confirming RDP (3389) exposed |
| `02-bruteforce-alerts.png` | Wazuh alerts for failed logons (rule 60122) |
| `03-correlation-alert.png` | Correlated brute-force alert (rule 60204, level 10) |
| `04-alert-detail.png` | Alert detail: source IP, target user, logon type |
| `05-no-successful-login.png` | Query confirming no successful logon (0 hits) |
| `06-sca-lockout-failed.png` | CIS SCA check 15507 (account lockout) failing |
| `07-lockout-applied.png` | Account lockout policy enabled on the endpoint |
| `08-attack-blocked.png` | Second attack stopped after 5 attempts |

![RDP brute-force alerts in Wazuh](../evidence/02-bruteforce-alerts.png)

## 6. Findings

- **Reconnaissance:** An `nmap -sV` scan from the attacker revealed only
  TCP **3389 (RDP)** open on the victim; the Windows firewall filtered
  the other 999 scanned ports.
- **Attack:** Hydra generated 50+ login attempts against user `user` over
  RDP in roughly two minutes, using a common-password list.
- **Detection:** Wazuh logged each failed attempt as **rule 60122
  (Logon Failure, level 5)** and correlated them into **rule 60204
  (Multiple Windows Logon Failures, level 10)**, mapped to
  **MITRE ATT&CK T1110 — Brute Force**.
- **Impact check:** A query for successful logons (rule 60106) after the
  attack returned **0 results** — the attacker did **not** gain access.
- **Root cause:** The CIS SCA module showed check **15507 (Account
  lockout threshold)** in a **Failed** state. The account never locked,
  which is why the attacker could try 50+ passwords without being stopped.

## 7. SOC Assessment

- **Classification:** Confirmed incident (unsuccessful attack)
- **Severity:** Medium — real attack, no compromise, exposed service
- **Confidence:** High — single source IP, high-volume dictionary attempts,
  clear timeline
- **Escalation:** Recommended to L2 for IP block and tuning review

**Justification:** The activity is unambiguously a brute-force attempt
(volume, timing, dictionary passwords, single source). It is classified
as a confirmed-but-unsuccessful incident because no successful logon
followed. Severity is Medium rather than High because there was no
compromise and the target is an internal lab host.

## 8. Recommended Actions

1. **Contain:** Block the attacker IP (10.10.99.102) at the firewall.
2. **Harden (root cause):** Enable the account lockout policy:
   `net accounts /lockoutthreshold:5 /lockoutduration:15 /lockoutwindow:15`
3. **Reduce exposure:** Restrict RDP to authorized management hosts only.
4. **Tuning:** Ensure brute-force correlation alerts are escalated, not
   buried among the individual level-5 events.
5. **Verify:** Re-run the attack and confirm the account locks.

## 9. Verification

After enabling the account lockout policy (threshold = 5), the attack was
repeated. This time Hydra was stopped after ~5 attempts: the account
locked, RDP began rejecting connections, and Hydra aborted with
connection errors (`all children were disabled due to too many
connection errors`). The Wazuh timeline clearly shows a large spike of
failed logons during the first attack and a much smaller one during the
second — visual proof the hardening worked.

## 10. Limitations

- RDP account-lockout events (4740) are primarily generated for domain
  accounts; on a standalone workstation the lockout was confirmed via the
  attacker's output and the reduced alert volume rather than a dedicated
  lockout event.
- The lab firewall (pfSense) was not yet forwarding logs to Wazuh, so
  the attack was observed mainly from the endpoint perspective. Adding
  firewall visibility would strengthen network-level detection.

## 11. Lessons Learned

- Alert **level does not equal incident severity**: a level-10 correlated
  alert was the real attack, while a level-12 alert in the same view was
  a benign Windows process.
- The most valuable SOC skill is **connecting detection to defense**: the
  SIEM detected the attack, the SCA module revealed *why* it was possible,
  and hardening closed the gap.
- Always confirm whether an attack **succeeded** (check for successful
  logons) before classifying and closing.

## 12. References

- Wazuh documentation — https://documentation.wazuh.com/
- MITRE ATT&CK T1110 (Brute Force) — https://attack.mitre.org/techniques/T1110/
- CIS Microsoft Windows 10 Benchmark — https://www.cisecurity.org/benchmark/microsoft_windows_desktop
