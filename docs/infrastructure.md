## Proxmox Host

The SOC lab is hosted on a Dell OptiPlex running Proxmox VE.

### Host Resources

- RAM: 15 GiB
- Existing VM: ubuntu-lab
- Existing containers: Jellyfin and docker-host
- Virtualisation platform: Proxmox VE

Before deploying the SIEM, host resource utilisation was checked to ensure
sufficient memory and storage were available for an additional Wazuh VM.
