#  Operation 002: "Deploy the Head Office"

##  The Scenario
The design blueprint from Operation 001 has been officially signed off by the Senior Engineer. Now, it is time to move from paper to production. The cabling team has finished running lines at the Chennai Head Office, the physical hardware is racked, and it is a completely greenfield deployment.

Your job is to bring the network live in Packet Tracer using the design parameters decided in the previous stage. 

---

##  Live Requirements

### Layer 2 Configuration
* Configure hostnames, banners, and access security.
* Provision **VLAN 10 (IT)**, **VLAN 20 (HR)**, and **VLAN 30 (Management)** on the switch.
* Assign appropriate access interfaces to their respective VLANs.
* Configure the uplink interface as an 802.1Q Trunk line.
* Administratively shut down and describe all unused ports.

### Layer 3 & Services Configuration
* Implement **Router-on-a-Stick (ROAS)** on the core router using subinterfaces matching the VLAN IDs.
* Configure dynamic DHCP pools on the router for all corporate subnets.
* Ensure complete Inter-VLAN routing functionality.

---

##  Deliverables
To complete this phase, you must upload:
1. Your completed `.pkt` topology diagram.
2. An updated **Device Inventory**, **VLAN Table**, and **IP Address Table**.
3. A realistic **Build Log / Incident Report** tracking any deployment bugs you encountered.