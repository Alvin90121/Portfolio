# SOC / SIEM Homelab

## Overview

This project documents the design and implementation of a small SOC-style
homelab using Proxmox VE and Wazuh.

The purpose of the lab is to develop practical experience with SIEM
monitoring, endpoint logging, alert triage, MITRE ATT&CK mapping and
basic incident investigation.

The environment currently contains a dedicated Wazuh SIEM server and
an Ubuntu endpoint monitored using the Wazuh agent.

## Objectives

- Deploy a dedicated Wazuh SIEM server.
- Configure centralised security monitoring.
- Onboard Linux and Windows endpoints.
- Generate controlled security events.
- Investigate alerts using a SOC-style workflow.
- Correlate SIEM alerts with raw endpoint logs.
- Map detections to MITRE ATT&CK.
- Document findings and remediation recommendations.

## Lab Environment

| Component | Purpose |
|---|---|
| Proxmox VE | Virtualisation platform |
| soc-siem | Wazuh SIEM server |
| ubuntu-lab | Linux monitored endpoint |
| Wazuh Manager | Processes security events |
| Wazuh Indexer | Stores and indexes alerts |
| Wazuh Dashboard | Security monitoring interface |
| Wazuh Agent | Endpoint telemetry collection |

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
