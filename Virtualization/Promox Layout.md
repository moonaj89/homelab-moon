# Proxmox Layout and Resource Planning

## Host Overview

- Hardware: Minisforum UM790 (Ryzen 9 7940HS)
- RAM: 64 GB DDR5
- Storage:
  - NVMe 1: 2 TB Samsung 990 PRO (Proxmox + VMs)
  - NVMe 2: (future) – Security Onion PCAPs / bulk VM storage

## Storage

- `local` (SSD1): ISO images, templates
- `local-lvm` (SSD1): primary VM disks
- (future) `so-pcaps` (SSD2): Security Onion PCAP/data volume
- (future) `nas-nfs`: NFS storage from dedicated NAS

## Planned VMs

| Name                     | OS                  | vCPU | RAM  | Notes                                |
|--------------------------|---------------------|------|------|--------------------------------------|
| pve-admin-jump           | Windows 11 / Linux  | 2    | 4 GB | Admin / jump box                     |
| so-all-in-one            | Ubuntu (SO)         | 4–8  | 20 GB| Security Onion all-in-one            |
| ad-dc01                  | Windows Server 2022 | 2    | 4 GB | Domain Controller                    |
| docker-services          | Ubuntu Server       | 2    | 4 GB | Docker host (NPM, Vaultwarden, etc.) |
| lab-linux-01             | Ubuntu Server       | 2    | 2 GB | General-purpose lab box              |

This document will evolve as the lab grows.
