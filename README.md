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
Pivoted to the pre-built OVA appliance instead of a manual install - more reliable path given the host issues above. *(update this section as you get it running)*

## 📚 What I'm learning

- SIEM architecture and log pipeline basics
- VM/hypervisor troubleshooting (turns out this is half the job)
- Wazuh dashboard, rules, and alerting

## 🔜 Next steps

- [ ] Get Wazuh OVA fully deployed and dashboard accessible
- [ ] Add a log source (e.g. a second VM as a monitored endpoint)
- [ ] Configure custom detection rules
- [ ] Document a sample incident/alert walkthrough


*Part of my cybersecurity home lab work while completing a Diploma of IT (Cyber Security).*
