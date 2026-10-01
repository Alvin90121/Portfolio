# Network Topology

## Overview

The SOC homelab runs inside my existing Proxmox VE environment.

A dedicated Wazuh server is used as the central SIEM platform, while separate virtual machines act as monitored endpoints.

The current environment contains:

- Proxmox VE as the virtualisation platform
- `soc-siem` as the central Wazuh server
- `ubuntu-lab` as the first monitored Linux endpoint
- Wazuh Agent for endpoint telemetry collection
- Wazuh Dashboard for alert analysis and investigation

## Current Architecture

```text
                         Home Network
                              |
                              |
                         eero Router
                        192.168.4.1
                              |
                              |
                       +-------------+
                       |   Proxmox   |
                       |     VE      |
                       +-------------+
                              |
                +-------------+-------------+
                |                           |
                |                           |
                v                           v
        +---------------+           +---------------+
        |   soc-siem    |           |  ubuntu-lab   |
        |               |           |               |
        | 192.168.7.70  |           | 192.168.7.164 |
        |               |           |  Wazuh Agent  |
        +-------+-------+           +-------+-------+
                ^                           |
                |                           |
                |     Security Events       |
                |     and Telemetry         |
                +---------------------------+
                |
                v
        +------------------+
        |  Wazuh Manager   |
        |  Wazuh Indexer   |
        |  Wazuh Dashboard |
        +------------------+
                |
                |
                v
        Analyst Web Browser
```

## Network Details

| Component | Address | Purpose |
|---|---|---|
| eero Router | `192.168.4.1` | Default gateway |
| Proxmox VE | Home network | Virtualisation host |
| soc-siem | `192.168.7.70/22` | Wazuh SIEM server |
| ubuntu-lab | `192.168.7.164/22` | Monitored Linux endpoint |

## SIEM Communication

The `ubuntu-lab` virtual machine runs the Wazuh Agent.

The agent collects security telemetry from the endpoint and sends it to the Wazuh Manager running on `soc-siem`.

Collected information includes:

- Linux system events
- authentication activity
- journald logs
- package information
- file integrity monitoring data
- endpoint inventory information

The Wazuh Manager analyses incoming events against its ruleset.

Matching security events are then indexed and made available through the Wazuh Dashboard for investigation.

## Analyst Access

The Wazuh Dashboard is accessed through HTTPS from a workstation on the local network.

```text
https://192.168.7.70
```

The dashboard provides access to:

- endpoint status
- security alerts
- threat hunting
- MITRE ATT&CK mappings
- vulnerability information
- file integrity monitoring
- security configuration information

## Future Architecture

The lab will be expanded to include additional endpoints and security telemetry sources.

Planned additions include:

```text
                       soc-siem
                           |
           +---------------+---------------+
           |               |               |
           v               v               v
      ubuntu-lab      Windows SOC      docker-host
                        Endpoint
                           |
                         Sysmon
```

The Windows endpoint will provide additional telemetry using:

- Wazuh Agent
- Windows Event Logs
- Microsoft Sysmon

This will allow the lab to support more realistic SOC detection and investigation scenarios.
