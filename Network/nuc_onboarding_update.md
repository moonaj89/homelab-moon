# 📘 Home Lab – Proxmox NUC Networking Baseline

## 📍 Phase Status

**Phase 4 COMPLETE – Proxmox Host Networking (Single-NIC, VLAN-aware)**
System is stable, reachable, and ready for VM onboarding.

---

## 🖥️ Hardware Overview

| Component     | Details                              |
| ------------- | ------------------------------------ |
| Hypervisor    | Proxmox VE 9.1                       |
| Host          | Minisforum NUC                       |
| Physical NICs | 1× onboard Ethernet (`nic0`)         |
| Secondary NIC | USB 3.0 → RJ45 (planned, not active) |

---

## 🌐 Network Topology Summary

### VLANs in Use

| VLAN ID | Purpose                       | Subnet            |
| ------- | ----------------------------- | ----------------- |
| 20      | Home LAN (wired)              | 10.10.20.0/24     |
| 30      | Home Wi-Fi                    | 10.10.30.0/24     |
| 40      | Guest Wi-Fi                   | 10.10.40.0/24     |
| 50      | Lab / IoT                     | 10.10.50.0/24     |
| **80**  | **Infrastructure Management** | **10.10.80.0/24** |
| 99      | Native / Transit              | N/A               |

---

## 🔌 Switch Port Assignments (TP-Link)

| Port  | Device            | PVID   | Tagged VLANs           |
| ----- | ----------------- | ------ | ---------------------- |
| 1     | PA-440            | 99     | 20, 30, 40, 50, 60, 80 |
| 2     | Laptop            | 20     | —                      |
| 3     | WAP               | 20     | 30, 40, 50             |
| 5     | Pi 5              | 50     | —                      |
| **6** | **NUC (Proxmox)** | **80** | **60**                 |
| 8     | Admin / Jump      | 80     | —                      |

> **Design principle:**
> *Untagged traffic = management plane*
> *Tagged traffic = workload / client plane*

---

## 🔐 Firewall (PA-440) Design

* Inter-VLAN routing via `ethernet1/7.x` subinterfaces
* Management traffic is **routed**, not bridged
* Explicit allow rules for:

  * VLAN 20 → VLAN 80 (admin access)
  * VLAN 80 → infrastructure services
* No NAT used internally

---

## 🧱 Proxmox Network Configuration (Final)

### Physical Interface

```
nic0  (active)
```

### Bridge Configuration

#### vmbr0 – Management + Trunk Bridge

| Setting      | Value                          |
| ------------ | ------------------------------ |
| Bridge ports | nic0                           |
| VLAN aware   | Yes                            |
| IPv4         | 10.10.80.81/24                 |
| Gateway      | 10.10.80.1                     |
| Purpose      | Proxmox mgmt + VM VLAN tagging |

✅ Single-bridge design (required for single-NIC hosts)
❌ No additional bridges created

---

## 🧠 Design Rationale

### Why only `vmbr0`

* Proxmox does **not** allow one NIC in multiple bridges
* VLAN tagging is handled **per VM NIC**, not per bridge
* This mirrors enterprise deployments for small hypervisors

### How VMs will be connected

When creating a VM:

* Bridge: `vmbr0`
* VLAN Tag: `60` (or others as needed)

Proxmox handles tagging → switch passes → PA-440 routes.

---

## 🛟 Recovery & Safety Controls

* **Port 8 (VLAN 80)** = break-glass admin access
* Management traffic remains **untagged**
* VLAN awareness enabled only after mgmt stability confirmed
* No pending host networking changes

---

## 🟢 Validation Checklist

* [x] Proxmox reachable from VLAN 20
* [x] Proxmox reachable from VLAN 80
* [x] Switch PVIDs validated
* [x] Firewall routing verified
* [x] VLAN awareness enabled safely
* [x] No lockouts during changes

---

## 🔜 Planned Next Phases

1. Phase 5 – Create test VM on VLAN 60
2. Phase 6 – Add USB NIC for SPAN / mirroring
3. Phase 7 – Security Onion deployment
4. Phase 8 – Logging, dashboards, detections

---

## 🧠 Key Lessons Captured

* PVID controls **untagged management traffic**
* WAP management traffic is untagged
* Single-NIC Proxmox hosts use **one VLAN-aware bridge**
* Trunking does **not** mean tagging management
* Always establish a break-glass path before risky changes
