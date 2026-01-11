# Proxmox Ubuntu Golden Template – Session Wrap-Up

## What Changed Tonight

### 1. Established a **true golden Ubuntu 22.04 template**
- Converted a working Ubuntu VM into an **immutable Proxmox template**

---

### 2. Integrated **cloud-init** correctly
- Installed and validated cloud-init inside the template
- Added a **Cloud-Init drive** via Proxmox
- Cleaned cloud-init state before templating so it runs **fresh on every clone**
- cloud-init now handles **first-boot identity**, not ongoing configuration

---

### 3. Fixed the “base clone SSH problem” permanently
- Eliminated shared machine identity by:
  - Resetting `/etc/machine-id`
  - Allowing SSH host keys to regenerate on first boot
- Each clone is now cryptographically a **unique machine**

---

### 4. Standardized on **SSH key–based access**
- One admin user
- One admin SSH key
- No passwords
- cloud-init injects the public key into every clone
- SSH access works consistently and predictably

---

### 5. Adopted a **bastion / jump-host model** until firewall rules are in place to leverage ssh from workstation in VLAN 20
- Proxmox node (NUC) acts as a trusted management host
- SSH from Proxmox → VMs works without firewall changes
- Direct workstation → VM SSH intentionally blocked by Palo Alto currently (expected and correct)

---

### 6. Confirmed clone behavior is deterministic
- New clones:
  - Get a hostname derived from VM name
  - Get a fresh SSH host identity
  - Trust the same admin SSH key
- Successfully validated by launching multiple test clones
- Identity separation became clear and repeatable

---

## What Was Learned (Key Takeaways)

### 1. **cloud-init is not about passwords**
- Passwords are incidental and often unnecessary
- cloud-init’s real job is **machine identity at first boot**
- It turns a cloned disk into a *new server*, not a copy

---

### 2. Username ≠ Hostname
- **Username** = human/admin identity (same everywhere)
- **Hostname** = machine identity (unique per VM)
- Hostname defaults to VM name in Proxmox unless overridden
- Mirrors how real server fleets operate

---

### 3. SSH user keys vs SSH host keys are different (and both matter)
- **User SSH key**
  - Proves *who you are*
  - Same key used across all servers
- **SSH host keys**
  - Prove *what server you are talking to*
  - Must be unique per VM
- cloud-init ensured host keys regenerate so trust is meaningful

---

### 4. Templates are versioned, not snapshotted
- Proxmox does not allow snapshots of templates by design
- Templates are **immutable artifacts**
- Changes are made by:
  - Cloning → modifying → converting to a new template version
- Matches AMIs, images, and golden builds in production

---

### 5. A scalable mental model is now in place
- **Template** = frozen, identity-less base
- **cloud-init** = identity factory
- **Clone** = real server at first boot
- **Proxmox** = management plane / bastion
- **Palo Alto** = policy boundary

---

## Current State (End of Session)

- Templates are complete and stable
- No changes pending
- Identity model intentionally simple:
  - One user
  - One admin key
- All VMs shut down cleanly
- Platform is ready for:
  - Service VMs
  - Docker hosts
  - Security tooling
  - Palo policy refinement (later)

---
