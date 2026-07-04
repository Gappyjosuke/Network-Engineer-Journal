[← Back ](/README.md)

# Original Blueprint Submission: Engineer Gappy

Here is the baseline production architecture submitted by the repository founder, along with the official peer-review notes from the Senior Network Engineer.

---

## Gappy's Original Network Blueprint

### Global Parameters

<div align="center">

| Parameter | Configuration / Value |
| :--- | :--- |
| **IP Domain-Name:** | `ungabhai.com` |
| **Banner MOTD:** | ***************************************************************************************** <br>WARNING: This is a private network system owned by UngaBhai Technologies.<br>Unauthorized access, use, or modification of this network device is <br>strictly prohibited. All traffic and activities are actively monitored, <br>recorded, and logged. Evidence of unauthorized use will be turned <br>over to law enforcement. Disconnect immediately if unauthorized.<br>******************************************************************************************|

</div>

---

### Hostname Standards

<div align="center">

| Component | Standard / Example |
| :--- | :--- |
| **Syntax Format:** | `[Location Code]-[Role/Device Type]-[Identifier]` |
| **Router:** | `MB-COR-RU-01` (Main Building - Core Router) |
| **Switch:** | `MB-ACCSW-01` (Main Building - Access Switch) |

</div>

---

### VLAN Mapping

<div align="center">

| Command / Configuration | Name Assignment |
| :--- | :--- |
| `vlan 10` | `name VLAN-10-IT` |
| `vlan 20` | `name VLAN-20-HR` |
| `vlan 30` | `name VLAN-30-MGMT` |

</div>

---

### IP Allocation Profile (VLSM Implementation)

Using network block `10.0.0.0 /8` partitioned to accommodate up to 100 targets per production tier for scale buffer:

<div align="center">

| Segment | Range & CIDR | Notes / Assignments |
| :--- | :--- | :--- |
| **HR Pool:** | `10.0.0.0 - 10.0.0.127 /25` | |
| **IT Pool:** | `10.0.0.128 - 10.0.0.255 /25` | |
| **Management Pool:** | `10.0.1.0 - 10.0.1.63 /28` | |
| **Point-to-Point Uplink Segment:** | `10.0.1.64 - 10.0.1.67 /30` | • *Switch Gi0/1 IP:* `10.0.1.65 255.255.255.252`<br>• *Router Gi0/1 IP:* `10.0.1.66 255.255.255.252` |

</div>

---

### Security & Device Access

<div align="center">

| Parameter | Security Configuration Command |
| :--- | :--- |
| **Local Account:** | `username ubadmin algorithm-type sha256 secret ungabhai@2026` |
| **Enable Secret:** | `enable algorithm sha256 secret ungabhai@2026` |

</div>

---

### Interface Descriptions & Port Policies

<div align="center">

| Device | Interface Range | Description Command |
| :--- | :--- | :--- |
| **Switch Allocations:** | `int ra fa0/1-4` | `des # MGMT-V30 #` |
| | `int ra fa0/5-12` | `des # HR-V20 #` |
| | `int ra fa0/13-22` | `des # IT-V10 #` |
| | `int g0/1` | `des # MB-ACCSW-01 Gi0/1 uplink to MAIN-ROUTER #` |
| **Router Allocations:** | `int g0/1` | `des # MB-COR-RU-01 Gi0/1 uplink to ACCSW01 #` |

</div>

---

### Unused Port Security Hardening

All unassigned interfaces are labeled explicitly and set to an administrative down state.

```ios
! Switch Cleanup
interface range fa0/23-24, gi0/2
 description # UNUSED-PORTS #
 shutdown

! Router Cleanup
interface range gi0/0, gi0/2
 description # UNUSED-PORTS #
 shutdown

---

[← Back ](/README.md)
