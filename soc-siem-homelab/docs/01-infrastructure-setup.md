# Infrastructure Setup

## Overview

I built the SOC lab on my existing Dell OptiPlex running Proxmox VE.

Because the Proxmox host already runs several workloads, I first checked its available memory and storage before creating another virtual machine.

I chose to deploy Wazuh on a dedicated VM rather than installing it alongside an existing service. This keeps my SIEM environment separate and makes resource management and troubleshooting easier.

## Proxmox Host

My Proxmox host already runs systems including:

- Jellyfin
- docker-host
- ubuntu-lab

Before deploying Wazuh, I checked the available memory using:

```bash
free -h
```

At the time of deployment, the host had approximately:

```text
15 GiB total RAM
11 GiB available RAM
```

I also checked the Proxmox storage pools using:

```bash
pvesm status
```

This helped me confirm that enough resources were available for the new SIEM VM.

## SIEM Virtual Machine

I created a dedicated virtual machine called:

```text
soc-siem
```

I configured it with the following resources:

| Setting | Value |
|---|---|
| VM ID | `103` |
| Hostname | `soc-siem` |
| Operating System | Ubuntu Server 24.04 LTS |
| CPU | 4 vCPU |
| Memory | 8 GB |
| Virtual Disk | 50 GB |
| Storage | local-lvm |
| Network Bridge | vmbr0 |
| Network Adapter | VirtIO |
| Static IP | `192.168.7.70/22` |
| Gateway | `192.168.4.1` |

I selected the `host` CPU type so the VM could use the processor features available on the physical Proxmox host.

## Evidence

The screenshot below shows the virtual machine configuration in Proxmox.

![Proxmox SIEM VM](../screenshots/01-soc-siem-proxmox-vm.png)

## Ubuntu Installation

I installed Ubuntu Server 24.04 LTS on the new VM.

I configured the hostname as:

```text
soc-siem
```

I also installed OpenSSH Server so I could manage the system remotely rather than relying only on the Proxmox console.

## Storage Issue

After installing Ubuntu, I checked the available filesystem space using:

```bash
df -h
```

Although the Proxmox virtual disk was configured as 50 GB, I noticed that the root filesystem was only approximately:

```text
24 GB
```

This showed that Ubuntu had not allocated the entire virtual disk to the root logical volume.

## LVM Investigation

I checked the volume group using:

```bash
sudo vgs
```

The output showed approximately:

```text
24 GB VFree
```

This confirmed that around half of the virtual disk was available but had not yet been assigned to the root logical volume.

## LVM Expansion

I expanded the root logical volume using:

```bash
sudo lvextend -l +100%FREE -r /dev/ubuntu-vg/ubuntu-lv
```

The `-r` option also resized the filesystem.

I then verified the change using:

```bash
df -h /
```

The usable root filesystem increased to approximately:

```text
48 GB
```

with around:

```text
39 GB
```

free before installing Wazuh.

## Static Networking

Initially, the VM received the DHCP address:

```text
192.168.7.202
```

Because monitored endpoints need a reliable destination for their telemetry, I decided to give the SIEM server a static IP address.

I configured:

```text
192.168.7.70/22
```

with the default gateway:

```text
192.168.4.1
```

The Ubuntu network interface was:

```text
ens18
```

## Connectivity Testing

After changing the networking configuration, I tested each part of the connection.

I tested access to the local gateway:

```bash
ping -c 3 192.168.4.1
```

I tested internet connectivity:

```bash
ping -c 3 1.1.1.1
```

I tested DNS resolution:

```bash
getent hosts packages.wazuh.com
```

All three tests completed successfully.

## Final Configuration

At the end of the infrastructure stage, my SIEM VM had the following configuration:

```text
Hostname:    soc-siem
IP Address:  192.168.7.70/22
Gateway:     192.168.4.1
CPU:         4 vCPU
Memory:      8 GB
Storage:     approximately 48 GB usable root filesystem
```

## What I Learned

This stage gave me practical experience with:

- Proxmox VM provisioning;
- Ubuntu Server installation;
- Linux LVM;
- filesystem expansion;
- static IP configuration;
- Linux networking;
- SSH administration;
- infrastructure troubleshooting.

One important lesson was that the virtual disk size shown by the hypervisor does not necessarily equal the usable filesystem capacity available inside the guest operating system.
