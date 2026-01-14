# Homelab Session Summary – Raspberry Pi 5 Recovery & Services Validation

## Overview
This session focused on restoring a Raspberry Pi 5–based services node after infrastructure changes, validating network configuration, confirming container health, and re-establishing awareness of the service orchestration layer in use. The goal was to ensure the node remained stable, predictable, and aligned with long-term homelab design principles.

This work intentionally prioritized **verification and cleanup** over introducing new services.

---

## Completed Tasks

### 1. Raspberry Pi 5 Power & Connectivity
- Powered on the Raspberry Pi 5 using a managed case with onboard power control
- Observed expected Ethernet connectivity during boot
- Identified an **unexpected Wi-Fi auto-connection**
- Disabled Wi-Fi to ensure the node operates as a wired-only server
- Confirmed system stability after boot

**Key commands used:**
```bash
nmcli device status
#show connections
nmcli connection show
#delete connection
sudo nmcli connection delete "<connection-name>"
```

## 2. Network Configuration Cleanup

- Verified current IP configuration on the Pi
- Confirmed the node had previously been configured with a static IP
- Reverted the Pi to DHCP-based addressing
- Implemented a DHCP reservation on the network gateway to retain a predictable address without host-level static configuration

**Key commands used (on the Pi):**
```
#show ip info, check default route, confirm ethernet details, dhcp vs static config
ip addr show
ip route
nmcli device show eth0
nmcli connection show "Wired connection 1"
#modify connection to set DHCP, clear status, and bounce interface
sudo nmcli connection modify "Wired connection 1" ipv4.method auto
sudo nmcli connection modify "Wired connection 1" ipv4.addresses ""
sudo nmcli connection modify "Wired connection 1" ipv4.gateway ""
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```

**Design rationale:**

- Centralized IP management
- Reduced configuration drift
- Easier future subnet or gateway changes

## 3. Docker Health Check (Post-Downtime)

- Verified Docker daemon status
- Confirmed containers automatically started after reboot
- Validated container uptime and exposed service ports
- Reviewed Docker disk usage and reclaimable space

**Key commands used:**
```
systemctl status docker --no-pager
docker info
docker ps -a
docker ps --format "table {{.Names}}\t{{.Image}}\t{{.Status}}"
docker system df
##show restart policies for docker containers
docker ps -aq | xargs -I{} docker inspect -f '{{.Name}}  ->  {{.HostConfig.RestartPolicy.Name}}' {}
```
No containers were found to be crash-looping or unhealthy

## 4. Application Data Sync Validation

- Corrected configuration for an existing photo/media synchronization workflow
- Re-ran synchronization
- Confirmed successful transfer and stable container behavior

Service-specific commands and paths are intentionally omitted from public documentation.

## 5. Firewall & Access Policy Review

- Audited recent network access rule changes
- Confirmed management access aligns with intended trust boundaries
- Validated connectivity only from approved internal networks

This step ensured changes made during earlier infrastructure work did not unintentionally expand access.

## 6. Service Management UI Rediscovery

- Re-identified the host-level service management platform in use.  I completely forgot configuring CasaOS previously.  But it explains the restart behavior for the containers I've been running.
- Confirmed it operates as system services, not as a container
- Located the management UI on a non-standard port - documented
- Resolved an initial UI loading issue
- Successfully accessed the interface from trusted internal networks

**Key commands used:**
```
systemctl status casaos*
```


**Key Learnings & Takeaways**
- Host-level orchestration tools may manage containers without appearing in docker ps
- Containers can reliably restart even without explicit Docker restart policies when managed externally
- DHCP with reservations provides the stability of static IPs without host-level configuration debt
- Wi-Fi should be disabled on infrastructure nodes unless explicitly required
- UI-layer issues (white screens, partial loads) are often service or cache related, not network failures
- Periodic rediscovery and documentation of “forgotten” tooling is a normal and valuable part of maintaining a mature homelab

**Current State (End of Session)**

- Raspberry Pi 5: Online, stable, wired-only, DHCP with reservation
- Docker: Healthy, all containers running as expected
- Networking: Predictable addressing and intended access boundaries
- Firewall: Policies reviewed and validated
- Service Management UI: Accessible and understood
- Documentation: Updated and cleaned for future reference