# Technology Stack & Infrastructure Requirements

This project integrates a variety of cloud and on-premise technologies to create a secure, scalable hybrid environment designed with a Zero-Trust posture.

### **Azure Cloud Services**
* **Networking:** Segmented Virtual Network (VNet) architecture featuring specialized subnets for Identity, Web, and Database tiers.
* **Hybrid Connectivity:** Established secure communication via an IPsec/IKEv2 Site-to-Site VPN tunnel.
* **Security & Traffic Control:** Utilized Network Security Groups (NSGs) for micro-segmentation to enforce a "Default Deny" posture and prevent lateral movement.
* **Identity & Governance:** Integrated Entra ID (formerly Azure AD) with Conditional Access MFA and Intune Device Compliance.
* **Modern Work & Scalability:** Deployed Azure Virtual Desktop (AVD) using Virtual Machine Scale Sets and FSLogix for persistent user profiles.
* **Storage:** Secured private-linked Azure Files shares using Private Endpoints.

### **On-Premise Infrastructure**
* **Virtualization:** Microsoft Hyper-V serves as the primary host for all local lab infrastructure.
* **Network Gateway:** pfSense (deployed as a Virtual Appliance) manages the local edge, firewall rules, and IPsec tunnel termination.
* **Physical Networking:** Utilized a physical unmanaged switch to bridge the Hyper-V host with the broader LAN and internet gateway.
* **Identity:** On-premise Active Directory Domain Services (AD DS) synchronized to the cloud via Entra Connect (AZ-ADSYNC-01).
* **Management:** Windows Admin Center (WAC) utilized for centralized, secure server administration.

### **Administrative & Troubleshooting Tools**
* **Network Mapping:** High-resolution Network Architecture diagrams created to visualize the hybrid traffic path.
* **Documentation:** Comprehensive technical write-ups produced to detail implementation strategies and project goals.
* **Path Validation:** Connectivity verified through ICMP path testing and troubleshooting of the Windows Guest Firewall and NSG layers.
