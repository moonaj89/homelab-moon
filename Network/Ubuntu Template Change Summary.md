# Change Log – Proxmox Ubuntu Golden Template (Session Summary)

**Scope:** Creation and stabilization of an Ubuntu 22.04 LTS golden template on Proxmox VE 9.1.1, including guest integration, SSH reliability, and template workflow corrections.

---

## Completed Accomplishments

- Successfully built an **Ubuntu Server 22.04 LTS** VM using:
  - UEFI (OVMF) firmware  
  - q35 machine type  
  - VirtIO SCSI disk controller  
  - VirtIO network adapter with VLAN tagging  
  - EFI disk on local-lvm storage  

- Enabled and validated **QEMU Guest Agent**:
  - Corrected misunderstanding around static service behavior  
  - Enabled guest agent at the Proxmox VM level before boot  
  - Verified guest agent functionality (IP reporting, graceful shutdown, clean VM state)  

- Resolved **serial console access issues**:
  - Identified Ubuntu Server’s default serial console behavior  
  - Correctly attached keyboard input in Proxmox 9.x console  
  - Confirmed reliable console access for headless VMs  

- Diagnosed and recovered from **hung VM / Proxmox lock conditions**:
  - Identified stuck KVM processes as root cause  
  - Safely cleared locks and restored VM operability  
  - Validated clean shutdown behavior post-fix  

- Corrected **SSH service failures on cloned VMs**:
  - Identified missing SSH host keys as cause of `sshd` startup failures  
  - Regenerated host keys and restored SSH functionality  
  - Ensured SSH service is installed, enabled, and stable in template  

- Identified and corrected **template base-disk immutability behavior**:
  - Discovered EFI/base disk read-only condition after template conversion  
  - Adopted correct Proxmox workflow: modify writable clone, not base image  
  - Created a new writable clone (VM 110) for final fixes  

- Finalized a **stable, production-ready Ubuntu template**:
  - Verified successful boot of cloned VMs  
  - Confirmed DHCP networking and SSH access from Proxmox host  
  - Validated guest agent and SSH functionality on fresh clones  

- Cleaned up environment:
  - Planned removal of obsolete templates and test clones  
  - Consolidated to a single known-good golden template  

- Established next-stage architecture decision:
  - Selected **cloud-init** as the solution for per-VM identity  
  - Planned elimination of shared credentials and hostnames  
  - Prepared for automated user, SSH key, and hostname injection  

---

## Outcome

A stable, modern, and reusable Ubuntu 22.04 LTS golden template was successfully created using Proxmox best practices. The environment is now ready for cloud-init integration to support scalable, secure VM provisioning without shared credentials or manual post-clone configuration.



## Lessons Learned

- **Enable QEMU Guest Agent in Proxmox before starting the VM**
  - The guest agent service inside Ubuntu is static and depends on a VirtIO channel created by Proxmox.
  - Starting or enabling the service before the Proxmox-side option is enabled leads to misleading errors and missing functionality.

- **Hung VMs are usually a host-side issue, not a guest issue**
  - Proxmox lock errors and failed shutdowns were caused by stuck KVM processes on the host.
  - Once the guest agent was functioning, clean shutdowns and reboots worked reliably.

- **Ubuntu Server defaults to a serial console**
  - Seeing `starting serial terminal on interface serial0` is normal behavior.
  - In Proxmox 9.x, keyboard input must be explicitly attached in the serial console.
  - Output visibility does not guarantee input is connected.

- **SSH host keys are required for `sshd` to start**
  - Deleting `/etc/ssh/ssh_host_*` without a guaranteed regeneration mechanism causes SSH to fail with `exit-code 1`.
  - For stability, ensure host keys exist in the template unless cloud-init is explicitly handling regeneration.

- **Templates are immutable base images**
  - Once a VM is converted to a template, its disks may become read-only base images.
  - Converting a template back to a VM does not always make disks writable again.
  - Correct workflow is to clone the template to a writable VM, apply changes, then re-template.

- **Proxmox GUI limitations require CLI familiarity**
  - Some actions (e.g., unsetting the template flag) are not exposed consistently in the GUI.
  - CLI commands (`qm set`, `qm template`) are sometimes the authoritative and safest method.

- **VM name, hostname, and SSH target are independent concepts**
  - Proxmox VM names are administrative labels only.
  - Linux hostnames persist across clones unless changed explicitly or via cloud-init.
  - SSH connects via IP/DNS, not Proxmox VM names.

- **Golden templates should avoid hard-coded identity**
  - User accounts, passwords, hostnames, and machine IDs should not be fixed in a template.
  - Cloud-init is the preferred mechanism to inject per-VM identity at first boot.

- **Stability first, purity second**
  - Ensuring services (SSH, guest agent) start reliably is more important than aggressive cleanup.
  - Identity cleanup (host keys, machine-id) should only be performed when automated regeneration is guaranteed.

---

## Key Takeaway

> **In Proxmox, treat templates as immutable bases.  
> Make changes in a writable clone, validate, then re-template.**

This approach avoids disk permission issues, service failures, and hard-to-debug boot errors.
