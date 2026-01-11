# Homelab Network Migration Overview (Sanitized Version)
### Modernizing a Flat Home Network into a Segmented, Switch-Based Architecture

---

## Summary

This document provides a high-level overview of my homelab network migration from a simple, directly connected topology toward a more scalable, enterprise-style design using VLANs, a managed switch, and a next-generation firewall.

This migration prepares the environment for future projects including virtualization, Security Onion, centralized storage, Windows domain services, and advanced automation.

---

## Why the Migration Was Needed

My previous setup consisted of devices connected directly to the firewall, with no switch and no meaningful segmentation. While simple, this approach had limitations:

### Limitations of the Old Design
- No separation between trusted, IoT, and guest devices  
- Limited ports and flexibility  
- Harder to scale as new devices/services are added  
- No ability to mirror traffic for monitoring or SOC tools  
- Not representative of modern enterprise network design  
- Difficult to automate or document cleanly  

To build a proper homelab—and grow my professional network engineering skills—I needed a more structured architecture.

---

## New Architecture: Switch-Based + VLAN Segmentation

The new design introduces a managed switch as the Layer 2 core, with the firewall performing Layer 3 routing between multiple logical networks.

### Key Concepts Implemented
- Managed Switch (L2 Core)  
- 802.1Q trunking  
- VLAN segmentation for isolation  
- Firewall-as-gateway routing  
- Dedicated management network  
- Segregated guest and IoT networks  
- Future-proof subnetting structure (details private)  

This design mirrors real enterprise environments and provides a solid foundation for advanced lab work.

---

## High-Level Topology (Sanitized)

