# SOC / SIEM Homelab

## Overview

I built this SOC/SIEM homelab to develop practical experience with security monitoring, endpoint logging, alert triage, MITRE ATT&CK and incident investigation.

I am using Proxmox VE as the virtualisation platform and Wazuh as the central SIEM.

My goal is to create a small environment where I can safely generate security events, analyse the resulting logs and investigate alerts using a workflow similar to what I would expect in a SOC environment.

The lab currently contains a dedicated Wazuh SIEM server and an Ubuntu endpoint monitored using the Wazuh Agent.

## Objectives

My objectives for this project are to:

- deploy and manage a dedicated SIEM server;
- configure centralised endpoint monitoring;
- onboard Linux and Windows endpoints;
- generate controlled security events;
- investigate alerts using a SOC-style workflow;
- correlate SIEM alerts with raw endpoint logs;
- understand MITRE ATT&CK mappings;
- practise alert classification and incident investigation;
- document my technical decisions and findings.

## Lab Environment

| Component | Purpose |
|---|---|
| Proxmox VE | Virtualisation platform for my lab |
| soc-siem | Dedicated Wazuh SIEM server |
| ubuntu-lab | Linux endpoint I monitor |
| Wazuh Manager | Processes and analyses security events |
| Wazuh Indexer | Stores and indexes alerts |
| Wazuh Dashboard | Interface used to investigate alerts |
| Wazuh Agent | Collects telemetry from monitored endpoints |

## Current Architecture

```text
                  Home Network
                       |
                    Proxmox
                       |
          +------------+------------+
          |                         |
          v                         v
      soc-siem                  ubuntu-lab
   192.168.7.70               192.168.7.164
          |                         |
          |       Wazuh Agent       |
          +<------------------------+
          |
          v
   Wazuh Dashboard
```

## Current Investigation

### Investigation 01 — Invalid SSH Login

For my first detection scenario, I generated a controlled SSH authentication attempt against `ubuntu-lab` using a non-existent username.

Wazuh detected the activity using rule `5710`.

During the investigation, I:

- identified the affected endpoint;
- reviewed the attempted username;
- identified the source IP;
- analysed the raw Linux authentication logs;
- correlated the host logs with the Wazuh alert;
- reviewed the Wazuh detection rule;
- examined the MITRE ATT&CK mappings;
- determined the final alert classification.

I classified the event as:

**True Positive — Benign / Authorised Security Test**

[Read the full investigation](investigations/01-invalid-ssh-login.md)

## Skills I Am Developing

Through this project, I am developing practical experience with:

- Wazuh SIEM
- Linux administration
- Proxmox VE
- centralised logging
- endpoint monitoring
- SSH log analysis
- alert triage
- event correlation
- MITRE ATT&CK
- incident investigation
- technical documentation

## Project Status

I am continuing to expand this lab.

Planned additions include:

- Windows endpoint monitoring
- Microsoft Sysmon
- repeated authentication / brute-force detection
- file integrity monitoring
- custom Wazuh rules
- vulnerability monitoring
- additional SOC investigation scenarios
