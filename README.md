# homelab-moon
mapping and documenting my homelab adventures

# Jacob's Homelab

This repository documents my personal homelab, used for:

- Network and firewall engineering practice
- Security monitoring and detection engineering
- Virtualization and systems administration
- Self-hosted services for my household

The lab is built to resemble a small enterprise environment, with VLANs, a next-gen firewall, dedicated switching, and a virtualization host.

---

## Current State High-Level Architecture

**Core components:**

- **Edge / Security**
  - ISP ONT + gateway
  - Palo Alto PA-440 (L3 firewall, VLAN gateway)

- **Switching**
  - TP-Link TL-SG2008 8 Port Gigabit Smart Managed Switch Omada (core L2 switch, VLAN trunk to PA)

- **Compute**
  - Minisforum UM790 (Ryzen 9 7940HS, 64 GB RAM, NVMe)
    - Proxmox VE hypervisor
    - Security Onion (planned)
    - Windows / Linux servers
  - Raspberry Pi 5 (Docker host for light services)

- **Wireless / Clients**
  - WAP: TP-Link EAP670 V2 Omada WiFi 6 AX5400 Wireless 2.5G
  - Laptops, phones, tablets, etc.

---

## Logical Segmentation

> All IPs and VLAN IDs here are lab examples, not production networks.

| VLAN | Name              | Subnet         | Zone             | Purpose                              |
|------|-------------------|----------------|------------------|--------------------------------------|
| 10   | Server / Compute  | 10.10.10.0/24  | `srv-zone`       | NUC, NAS (future), AD, Security VMs  |
| 20   | Home LAN (Wired)  | 10.10.20.0/24  | `home-wired-zone`| Wired clients and admin workstation  |
| 30   | Home Wi-Fi        | 10.10.30.0/24  | `home-wifi-zone` | General Wi-Fi devices                |
| 40   | Guest Wi-Fi       | 10.10.40.0/24  | `guest-wifi-zone`| Isolated guest access                |
| 50   | Lab / Automation  | 10.10.50.0/24  | `lab-iot-zone`   | Raspberry Pi, lab services, tooling  |
| 60   | NUC		   | 10.10.50.0/24  | `NUC`            | VM Workloads		      |
| 70   | Future NAS        | 10.10.70.0/24  | `NAS`            | Storage			      |
| 80   | Management        | 10.10.80.0/24  | `mgmt-zone`      | Switch / firewall / host management  |

See [`network/vlan-design.md`](network/vlan-design.md) for more detail.

---

## Contents of This Repo

- [`Change-Logs/`]('Change Log/') – Summary and lessons learned from variou efforts
- [`Docs/`](Docs/) – Overview, goals, and roadmap
- [`Network/`](Network/) – Topology, VLANs, IP plan
- [`Firewall/`](Firewall/) – Palo Alto zones, policies, and runbooks
- [`Virtualization/`](Virtualization/) – Proxmox layout, VM inventory, Security Onion notes
- [`Docker/`](Docker/) – Self-hosted services and `docker-compose` files
- [`Security-lab/`](Security-lab/) – Attack and detection lab notes
- [`Automation/`](Automation/) – Future Ansible, scripts, infra-as-code ideas
- [`Runbooks/`](Runbooks/) – Step-by-step procedures for changes and maintenance

---

## Security & Privacy Notes

- No real passwords, keys, tokens, or secrets are stored in this repo.
- IP addresses and hostnames are either generic or lab-only.
- Any resemblance to production environments is intentional for learning, but sanitized.

---

## Future Plans

- Add a NAS and integrate with Proxmox as a storage backend
- Expand Security Onion usage and document detection engineering
- Add Ansible playbooks for repeatable deployments
- Publish more detailed lab guides and blog-style writeups

