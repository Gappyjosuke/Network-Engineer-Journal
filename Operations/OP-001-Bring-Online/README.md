#  Operation 001: " Bring UngaBhai Technologies Online "

##  The Scenario
Welcome to the team. UngaBhai Technologies has just moved into its brand new Chennai Head Office. Absolutely nothing exists yet : 
no IP allocation scheme, no VLAN layouts, no device naming architecture, and zero documentation. 

Your task is to build the blueprint for the company's baseline network from Day 1 so we don't regret it six months down the line when scaling hits.

> [!TIP]
>  **Senior Engineer's Note:** Real engineers do not start inside the CLI. We start on paper. Do not open Packet Tracer until your design document is completely signed off.

---

##  Business Requirements

### Department Allocations
<div align="center">
  
| Department | Current Employees |
| :--- | :--- |
| **HR** | 5 Employees |
| **IT** | 8 Employees |
| **Management** | 2 Employees |

</div>

### Core Objectives & Constraints
*   **Segmentation:** All departments must be logically separated onto their own broadcast domains.
*   **Connectivity:** Management needs full access to all zones. HR and IT must be able to talk directly (do not apply ACL layers yet).
*   **Scope:** Internet connectivity is completely out of scope for this ticket.
*   **Scalability:** Address architecture must factor in future head-count expansion.
*   **Static Mapping:** No dynamic DHCP server is allocated yet. All initial endpoints utilize static assignments.

---

##  Hardware Allocation
*   `1` Enterprise Router
*   `1` Layer-2 Managed Access Switch
*   `15` Workstation PCs

---

##  Deliverables
To complete this challenge, create a copy of `DESIGN_TEMPLATE.md`, fill out your engineering choices, and save it under the `Solutions/` folder as `yourusername-solution.md`. 

Your design must define:
1. Base Network Subnet choice (`192.168.X.0/24` or `10.X.X.0/24`) and VLSM breakdown.
2. VLAN ID mappings and names.
3. Hostname scheme for future multi-branch identification.
4. Description tracking standard for live and dead interface ports.