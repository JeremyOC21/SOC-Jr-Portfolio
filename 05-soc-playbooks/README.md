# SOC Playbooks

## Objective

Repeatable, step-by-step procedures for common SOC Level 1 tasks. Each
playbook turns an investigation into a clear, ordered checklist so that
every alert is handled consistently, regardless of who is on shift.

These playbooks are based on cases actually performed in this lab (see
projects 02 and 04).

---

## Playbook 1 — General alert triage

**When to use:** any new alert that appears in the SIEM.

1. **Read the alert.** Note the rule ID, level, description, agent/host,
   and timestamp (convert UTC to local time if needed).
2. **Assess the level, but do not trust it blindly.** Alert level is a
   starting point, not the final severity (see "Key principle" below).
3. **Confirm the source.** Who/what generated it — a user, a service, a
   process, an external IP?
4. **Decide the path:**
   - Clearly benign and expected → document and close.
   - Suspicious or unclear → investigate further (Playbook 2 or 3).
   - Clearly malicious → contain and escalate (Playbook 4).
5. **Document** the decision and the evidence that supports it.

**Key principle:** alert level ≠ incident severity. In this lab a
level-10 correlated alert was a real attack, while a level-12 alert in
the same view was a benign Windows process. Context decides.

---

## Playbook 2 — Brute-force / credential attack response

**When to use:** multiple failed logons, "Multiple authentication
failures", or a correlated brute-force alert (e.g., Wazuh rule 60204,
MITRE T1110).

1. **Open the correlated alert** and confirm the pattern (many failures,
   short time window, single source).
2. **Identify the source IP** and the **target account** from the alert
   detail (e.g., `data.win.eventdata.ipAddress`).
3. **Pivot on the source IP** to see everything it did:
   `data.srcip:"<IP>"` or `data.win.eventdata.ipAddress:"<IP>"`.
4. **Check for a successful login after the attempts** (e.g., Wazuh rule
   60106). This is the critical question — did they get in?
   - 0 results → attack **unsuccessful**.
   - A success → **compromise**, escalate immediately.
5. **Classify:** confirmed incident (successful / unsuccessful) or benign.
6. **Contain** (if malicious): block the source IP at the firewall;
   consider disabling or forcing a reset on the targeted account.
7. **Recommend hardening:** enable account lockout, restrict the exposed
   service (e.g., RDP) to authorized hosts, enforce strong passwords/MFA.
8. **Document** in the incident report and escalate per Playbook 4.

---

## Playbook 3 — Suspicious process / false-positive handling

**When to use:** a process-based alert (unusual PowerShell, script
creation, registry change) that could be malicious or benign.

1. **Read the key fields:** `image` (what ran), `commandLine` (how), 
   `parentImage` (what launched it), `user` (with what privileges),
   `targetFilename` / `targetObject` (what it touched).
2. **Pivot on the process identifier** (Sysmon `ProcessGuid`) to connect
   the event to its parent and children and reconstruct the chain.
3. **Judge by context, not the command alone.** Ask: is the path
   legitimate (e.g., `System32`, a known vendor folder)? Does the parent
   make sense? Is it the normal time and user?
4. **Classify:**
   - Legitimate process, authorized activity → **false positive**.
   - Unknown binary from `Temp`/`AppData`, odd parent, off-hours →
     **suspicious**, investigate further or escalate.
5. **If false positive:** document the reasoning and recommend a tuning
   exception so the noise stops.
6. **If suspicious:** preserve evidence and escalate per Playbook 4.

**Example from this lab:** a SYSTEM-level PowerShell creating a script in
Temp (level 9) looked suspicious, but pivoting on the ProcessGuid showed
it was the Wazuh agent's own SCA module running `secedit /export`. The
same command could be malicious elsewhere (T1201) — context made it
benign.

---

## Playbook 4 — Escalation

**When to use:** the alert is a confirmed or likely incident, or is
beyond L1 scope.

**Escalate when:**
- A successful compromise is confirmed (successful malicious login,
  malware execution, data access).
- The activity affects a critical asset or spreads across hosts.
- The investigation needs tools or permissions beyond L1.
- You are unsure and the potential impact is high.

**What to include when escalating:**
- Short summary of what happened.
- Affected host(s) and account(s).
- Source IP and timeline (with timezone).
- Evidence: rule IDs, event IDs, screenshots.
- Classification, severity, and confidence, with justification.
- Containment already taken and what you recommend next.

**Do not:** classify something as malicious without evidence, or close an
incident before confirming whether the attack succeeded.

---

## Evidence collection checklist

For every case, capture enough to make it reproducible by another
analyst:

- [ ] The alert(s): rule ID, level, description, timestamp.
- [ ] Source and destination (IP, host, user).
- [ ] Supporting events (event IDs, log lines).
- [ ] Screenshots with the relevant fields visible.
- [ ] The query used to find the evidence.
- [ ] Timezone noted (SIEM often logs in UTC).

**Always sanitize** evidence before sharing: remove passwords, tokens,
and any sensitive data.

---

## References

- Wazuh documentation — https://documentation.wazuh.com/
- MITRE ATT&CK — https://attack.mitre.org/
- MITRE ATT&CK T1110 (Brute Force) — https://attack.mitre.org/techniques/T1110/
