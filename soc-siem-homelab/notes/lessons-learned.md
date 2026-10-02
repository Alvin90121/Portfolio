# Lessons Learned

## Overview

I use this document to record the technical lessons and troubleshooting experience I gain while building my SOC/SIEM homelab.

One of the main things I have learned so far is that building a monitoring environment involves much more than installing the SIEM itself.

Storage, networking, endpoint configuration and alert interpretation all affect whether the environment works correctly.

## Virtual Disk Size vs Usable Filesystem Size

When I created `soc-siem`, I assigned the VM a 50 GB virtual disk in Proxmox.

After installing Ubuntu, I checked the filesystem and noticed that the root filesystem was only approximately:

```text
24 GB
```

I investigated the LVM configuration using:

```bash
sudo vgs
```

and found that approximately another 24 GB was available in the volume group.

I expanded the logical volume using:

```bash
sudo lvextend -l +100%FREE -r /dev/ubuntu-vg/ubuntu-lv
```

After the change, the root filesystem increased to approximately:

```text
48 GB
```

### What I Learned

I learned that the virtual disk size configured in the hypervisor does not necessarily represent the amount of storage immediately available to applications inside the guest operating system.

I should always check the actual filesystem capacity before deploying storage-intensive applications.

## Static Networking

My Wazuh server initially received the DHCP address:

```text
192.168.7.202
```

Because the Wazuh agents need a reliable address for the manager, I changed the server to:

```text
192.168.7.70/22
```

### What I Learned

I learned that infrastructure services such as a SIEM benefit from stable addressing because other systems depend on them.

## Testing Connectivity at Multiple Layers

After changing the network configuration, I did not rely on a single ping test.

I tested the local gateway:

```bash
ping -c 3 192.168.4.1
```

I tested external connectivity:

```bash
ping -c 3 1.1.1.1
```

I tested DNS resolution:

```bash
getent hosts packages.wazuh.com
```

### What I Learned

I learned that networking problems can occur at different layers.

For example, a system could have:

- local connectivity but no internet access;
- internet access but broken DNS;
- a valid IP address but an incorrect default route.

Testing each part separately makes troubleshooting easier.

## Verifying Services After Installation

After installing Wazuh, I manually checked the core services:

```bash
systemctl is-active wazuh-manager wazuh-indexer wazuh-dashboard
```

All three returned:

```text
active
```

I also verified that HTTPS port 443 was listening.

### What I Learned

I learned not to assume that an installation is successful simply because the installer reaches the end without errors.

I should independently verify:

- service state;
- listening ports;
- application accessibility.

## Correlating Endpoint Logs With SIEM Alerts

For my SSH investigation, I first reviewed the raw Linux authentication logs.

The endpoint showed:

```text
Invalid user soc-test-user from 192.168.7.70
```

Wazuh then generated rule:

```text
5710
```

with the description:

```text
sshd: Attempt to login using a non-existent user
```

### What I Learned

I learned that SIEM alerts are more useful when I can trace them back to the underlying telemetry.

By comparing the host log and the central alert, I could confirm that Wazuh had correctly interpreted the event.

## An Alert Does Not Automatically Mean an Attack

The SSH event triggered a real Wazuh security rule.

However, after investigating the source address, I confirmed that the activity came from my own `soc-siem` host and had been deliberately generated.

I classified the event as:

```text
True Positive — Benign / Authorised Security Test
```

### What I Learned

I learned that an alert tells me that potentially interesting behaviour occurred.

It does not automatically tell me:

- whether the activity was malicious;
- whether a system was compromised;
- whether an attacker was involved;
- whether I have a security incident.

I need context before making that decision.

## True Positive Does Not Always Mean Malicious

Before this lab, it would have been easy to treat "true positive" as meaning that a genuine attack occurred.

The SSH investigation helped me understand the distinction.

The Wazuh detection was accurate because the behaviour really happened.

The activity itself was still authorised.

### What I Learned

I now separate two questions:

```text
Did the detected behaviour actually occur?
```

and:

```text
Was the activity malicious, suspicious, benign or authorised?
```

That distinction is important during alert triage.

## MITRE ATT&CK Context

Wazuh mapped the SSH event to:

```text
T1110.001 — Password Guessing
T1021.004 — SSH
```

The associated tactics included Credential Access and Lateral Movement.

### What I Learned

I learned that a MITRE ATT&CK mapping provides context about techniques associated with an event.

It does not prove that an attacker intentionally performed those techniques.

I still need to combine the mapping with other evidence before drawing a conclusion.

## Documentation

I have been collecting screenshots and recording commands while building the environment.

So far, I have documented:

- Proxmox infrastructure;
- Wazuh installation;
- the Wazuh Dashboard;
- Linux endpoint onboarding;
- raw SSH authentication logs;
- Wazuh alert details.

### What I Learned

I learned that documenting the environment as I build it is much easier and more accurate than trying to reconstruct everything later.

It also gives me evidence of the actual work I performed.

## Future Lessons

I will continue updating this file as I add:

- Windows event logging;
- Sysmon;
- repeated authentication detections;
- custom Wazuh rules;
- File Integrity Monitoring;
- vulnerability monitoring;
- incident response scenarios;
- detection engineering.
