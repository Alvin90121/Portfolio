---


```markdown
# Infrastructure Setup

## Overview

The SOC lab is hosted on a Dell OptiPlex running Proxmox VE.

A dedicated virtual machine was created for the Wazuh SIEM instead of
installing Wazuh on an existing application server.

## Wazuh VM

| Setting | Value |
|---|---|
| VM ID | 103 |
| Hostname | soc-siem |
| Operating System | Ubuntu Server 24.04 LTS |
| CPU | 4 vCPU |
| Memory | 8 GB |
| Storage | 50 GB |
| Network Bridge | vmbr0 |
| Static IP | 192.168.7.70/22 |
| Gateway | 192.168.4.1 |

## Storage Configuration

During the initial Ubuntu installation, approximately 24 GB of the
50 GB virtual disk was allocated to the root logical volume.

The remaining LVM space was identified using:

```bash
sudo vgs
