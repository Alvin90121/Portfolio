# Network Topology

## Overview

I built the SOC homelab inside my existing Proxmox VE environment.

I use a dedicated virtual machine for Wazuh so that my SIEM is separated from my other services and lab systems.

The current environment contains:

- Proxmox VE as my virtualisation platform;
- `soc-siem` as my central Wazuh server;
- `ubuntu-lab` as my first monitored Linux endpoint;
- Wazuh Agent for endpoint telemetry;
- Wazuh Dashboard for alert analysis and investigation.

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
                v
        Analyst Web Browser
```

## Network Details

| Component | Address | Purpose |
|---|---|---|
| eero Router | `192.168.4.1` | Default gateway |
| Proxmox VE | Home network | Hosts my lab systems |
| soc-siem | `192.168.7.70/22` | Central Wazuh SIEM |
| ubuntu-lab | `192.168.7.164/22` | Monitored Linux endpoint |

## SIEM Communication

I installed the Wazuh Agent on `ubuntu-lab`.

The agent collects security telemetry from the endpoint and sends it to the Wazuh Manager running on `soc-siem`.

The telemetry includes information such as:

- Linux system events;
- authentication activity;
- journald logs;
- package information;
- file integrity monitoring data;
- endpoint inventory information.

The Wazuh Manager analyses incoming events against its ruleset.

When an event matches a rule, Wazuh indexes the alert and makes it available through the dashboard for investigation.

## Analyst Access

I access the Wazuh Dashboard from a web browser on my local network.

```text
https://192.168.7.70
```

I use the dashboard to review:

- endpoint status;
- security alerts;
- threat hunting data;
- MITRE ATT&CK mappings;
- vulnerability information;
- file integrity monitoring;
- security configuration information.

## Future Architecture

I plan to expand the environment by adding a Windows endpoint and additional telemetry sources.

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

For the Windows endpoint, I plan to use:

- Wazuh Agent
- Windows Event Logs
- Microsoft Sysmon

This will allow me to investigate a wider range of endpoint activity and create more realistic SOC scenarios.
