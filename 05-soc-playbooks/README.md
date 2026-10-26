# SOC Playbooks

## Status: Planned

## 1. Objective

Create repeatable, step-by-step procedures (playbooks) for common SOC L1
tasks: alert triage, evidence collection, escalation, and reporting.

## 2. Why playbooks matter

In a real SOC, analysts follow playbooks so that every alert is handled
consistently, regardless of who is on shift. A good playbook turns a
complex investigation into a clear, ordered checklist.

## 3. Planned playbooks

- **Alert triage:** how to prioritize by severity, confirm the source,
  and decide benign / suspicious / escalate.
- **Brute-force response:** detect, confirm the source IP, check for a
  successful login, contain, and document (based on project 04).
- **False-positive handling:** how to verify, document, and request
  tuning.
- **Evidence collection:** what to capture, how to timestamp, and how to
  keep it reproducible.
- **Escalation:** when and how to escalate to L2, and what information to
  include.

## 4. Example — brute-force triage (draft)

1. Open the correlated alert (e.g., multiple logon failures).
2. Identify the source IP and target account from the alert detail.
3. Pivot on the source IP to see all activity from it.
4. Check for a **successful** login after the attempts.
5. Classify: confirmed incident / unsuccessful / benign.
6. If malicious: recommend blocking the IP and hardening the host.
7. Document in the incident report template and escalate if needed.

## 5. Status note

This project is planned. The brute-force playbook is partially validated
through the project 04 incident investigation.

## 6. References

- Wazuh documentation — https://documentation.wazuh.com/
- MITRE ATT&CK — https://attack.mitre.org/
