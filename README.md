# Wazuh SIEM Home Lab

A home lab project setting up Wazuh (open-source SIEM/XDR) to learn security monitoring, log analysis, and threat detection hands-on.

## 🎯 Goal

Build a working SIEM environment to practice the skills used in SOC analyst roles - log ingestion, alerting, and incident triage - as part of my transition into cybersecurity.

## 🖥️ Environment

- **Host:** Lenovo Yoga Slim 7i (Windows, AMD, 32GB RAM)
- **Hypervisor:** VirtualBox
- **SIEM:** Wazuh (deployed via official OVA appliance)

## 🚧 Build Log

### Attempt 1: Ubuntu VM + manual Wazuh install

Ran into persistent VM boot instability - graphics controller issues specific to the AMD host. Tried several fixes:

- Switching graphics controller to VMSVGA
- Booting with `nomodeset`
- Disabling EFI
- Disabling 3D acceleration

None resolved it reliably enough to build on top of.

### Attempt 2: Official Wazuh OVA appliance

Pivoted to the pre-built OVA appliance instead of a manual install - more reliable path given the host issues above. Deployed successfully, dashboard accessible, and the Windows host enrolled as a monitored agent.

## 🔍 Incident walkthrough: simulated authentication failure

### Test performed

To confirm the SIEM was actually detecting something rather than just running, I generated a controlled failed-login event on the monitored Windows host: 5 incorrect PIN attempts entered at the login screen, back to back.

This isn't a network-based brute-force attack — it's a local authentication failure test. Windows logs any failed login (including a wrong PIN) as Event ID 4625, and the Wazuh agent forwards that to the manager for correlation.

### What Wazuh detected

All 5 attempts triggered **rule 60122** — Wazuh's built-in rule for `Logon Failure - Unknown user or bad password`, mapped from Windows Event ID 4625.

| Field | Value |
|---|---|
| Rule ID | 60122 |
| Rule level | 5 |
| Description | Logon Failure - Unknown user or bad password |
| Source event | Windows Event ID 4625 |
| MITRE ATT&CK | T1078 (Valid Accounts), T1531 (Account Access Removal) |
| Alerts generated | 5 |

[screenshot: Wazuh dashboard filtered to rule.id:60122, showing the 5 alerts]

### Reading the alert

Rule level 5 is a low-severity single-event alert — Wazuh isn't correlating these 5 failures into a single "attack" alert on its own. That's expected: the default ruleset only escalates to a higher-severity brute-force alert when failures are correlated by rule (e.g. multiple 60122 hits from the same source within a timeframe), which requires either a higher volume of attempts or a custom correlation rule layered on top.

That gap is worth noting as a finding, not a failure: out of the box, Wazuh logs individual failures at low severity. Turning "5 failed logins" into "this looks like a brute-force attempt" is a tuning step, not something that happens automatically. It's also the next thing on my list — see below.

### What a SOC analyst would do next

- Check whether the failed attempts came from the expected user/session or an unfamiliar source
- Look for a following successful login (event 4624) from the same account, which would indicate the attempt eventually succeeded
- If this were a real alert, correlate the account against other activity in the same timeframe before deciding whether to escalate

### CVEs identified

[Vulnerability Detection module, host: (Windows agent name), findings: (CVE numbers), affected software: (name/version)]

*Fill in from the Wazuh Vulnerability Detection module for this agent.*

### Next: tuning a real brute-force correlation rule

Rule 60122 alerts on every single failure. A proper brute-force detection needs a correlation rule that groups multiple 60122 hits by source/user within a timeframe and raises severity — closer to what a SOC would actually escalate on. Building that rule is the next item on this lab.

## 📚 What I'm learning

- SIEM architecture and log pipeline basics
- VM/hypervisor troubleshooting (turns out this is half the job)
- Wazuh dashboard, rules, and alerting

## 🔜 Next steps

- [x] Get Wazuh OVA fully deployed and dashboard accessible
- [x] Add a log source (Windows host as a monitored agent)
- [x] Document a sample incident/alert walkthrough
- [ ] Fill in CVE findings from Vulnerability Detection
- [ ] Build a custom correlation rule for brute-force detection (beyond single-event rule 60122)

*Part of my cybersecurity home lab work while completing a Diploma of IT (Cyber Security).*
