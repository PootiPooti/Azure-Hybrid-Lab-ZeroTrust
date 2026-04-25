# Hybrid Cloud & Zero-Trust Lab: Architecture & Implementation
**By: Raul Rios**

**Focus:** Cloud Networking | Identity | Scalable Infrastructure

---

## Project Goal
I built this lab to bridge the gap between "knowing" Azure and "doing" Azure. It features a live **Site-to-Site VPN** connecting my **Hyper-V home lab** to an **Azure VNet**, all managed under a **Zero-Trust** framework so every connection has to be verified.

## Architecture & Connectivity
Instead of just a basic flat network, I built this as a **Segmented VNet architecture** that is already "Hub-and-Spoke ready."

* **IP Schema:** I went with a non-overlapping IP schema and isolated my identity and gateway services. This makes the whole setup scalable and easy to peer without having to rip anything out later.
* **Subnet Segmentation:** I carved out core subnets for Identity, Web, and DB. I gave the `workloadSubnet` and `vdiSubnet` full **/24 blocks** so they have plenty of room to grow, while keeping the specialized service subnets tight at **/27** to stay organized.
* **The Legacy Tier (On-Prem):** On the Hyper-V side, I kept a legacy environment with a combined App/DB VM and a Test Client. This represents the older servers I’m systematically moving over and refactoring into a modern, tiered setup in Azure.
* **The S2S Tunnel:** I set up an **IPsec/IKEv2 VPN tunnel** from my home network to the Azure `GatewaySubnet`. Getting the parameters to match was a bit of a headache at first, but once it connected, the replication between my local Hyper-V host and the Azure **AZ-DC-01** was rock solid.
* **Modern Management:** I moved away from "RDP-everywhere" by deploying **AZ-WAC-01 (Windows Admin Center)**. To keep things secure, I locked down the management interface so it’s only accessible from the **AZ-jumpbox**, creating a controlled "Admin-Only" path for the whole workload tier.

## Identity & Security (Zero-Trust)
Since I’m focusing on the identity side of things, I used **AZ-ADSYNC-01** to bridge my local AD to **Entra ID**. This let me really dig into the "Identity is the new perimeter" concept and build security around the user rather than just the network.

* **Conditional Access:** I set up a policy so that if I’m not on my home VPN (my "Trusted Location"), I get prompted for MFA immediately. It’s a great way to see how identity-based rules actually work in a live environment.
* **Intune & Compliance:** I linked my test devices to **Intune** to enforce some rules. If a device isn't compliant—like if I turn BitLocker off—**Entra ID** automatically blocks it from hitting my private-linked Azure Files shares.
* **VDI Scalability:** I deployed **AZ-VDI-SS** as a Virtual Machine Scale Set to simulate a remote workforce. It’s a cool setup because I’m using **FSLogix profiles** stored on **Private Endpoints** to keep user data persistent, even though the VMs themselves are stateless.

## Network Topology
<details>
  <summary><b> View Full Network Topology (Click image to open high-res)</b></summary>
  <br>
  <a href="https://github.com/user-attachments/assets/404aae40-b6f4-400e-a1f7-56965fd5583a" target="_blank">
    <img src="https://github.com/user-attachments/assets/404aae40-b6f4-400e-a1f7-56965fd5583a" alt="Hybrid Azure Lab Diagram" width="100%">
  </a>
  <p align="center"><i>Click the diagram above to open the full-size image in a new tab.</i></p>
</details>

## Troubleshooting & Lessons Learned
* **The Routing Gap:** I initially couldn't get my local VMs to talk to Azure. It ended up being a mix of an improperly sized GatewaySubnet and a missing NSG rule that was dropping the traffic.
* **Firewall Layers:** This taught me that cloud-level NSG rules are only half the battle. If the Windows Guest Firewall is still dropping ICMP, your "Allowed" rule doesn't mean much—you have to troubleshoot the entire path.
* **Micro-segmentation:** Putting an NSG "Shield" on every single subnet border was tedious, but it was the only way to truly enforce a "Default Deny" posture and stop lateral movement.


## Key Takeaways
Building this really cleared up how **User Defined Routes (UDRs)** work and why segmenting traffic by role is so much better than just dumping everything into one big subnet. I designed this foundation to look like a "Small Business" setup today, but it’s completely "Enterprise Ready" for tomorrow.

---

## Future Roadmap
* **Scale to Hub-and-Spoke:** Peer the current VDI and Web tiers into dedicated Spoke VNets.
* **Centralized Security:** Implement **Azure Firewall** or an NVA for centralized traffic inspection.
* **Infrastructure as Code (IaC):** Automate the entire stack using **Bicep** or **Terraform** for version-controlled, repeatable deployments.
