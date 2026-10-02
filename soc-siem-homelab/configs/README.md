# Configuration Files

## Overview

This directory is used to store sanitised configuration files that I create or modify as part of my SOC/SIEM homelab.

My aim is to show not only the final lab environment, but also how I configured the tools used throughout the project.

## Planned Configuration Files

As I expand the lab, I expect to add examples such as:

```text
Wazuh agent configuration
Sysmon configuration
Custom Wazuh detection rules
File Integrity Monitoring configuration
Linux logging configuration
```

## Security

Before uploading any configuration file, I will review it to make sure it does not contain sensitive information.

I will not commit:

- passwords;
- API keys;
- authentication tokens;
- SSH private keys;
- certificate private keys;
- secret values;
- personal information.

## Planned Structure

As the project develops, I may organise this directory like this:

```text
configs/
│
├── wazuh/
│   ├── custom-rules.xml
│   └── agent-config.xml
│
├── sysmon/
│   └── sysmon-config.xml
│
└── linux/
    └── logging-notes.md
```

I will add these files when I reach the relevant stages of the project rather than uploading configuration files that I have not actually used.
