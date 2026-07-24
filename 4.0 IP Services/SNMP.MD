[*← Back to CCNA Index*](../README.MD)

# Simple Network Management Protocol (SNMP)

**Simple Network Management Protocol (SNMP)** is an application-layer protocol used to monitor, manage, and configure network devices such as routers, switches, firewalls, servers, printers, and access points from a centralized **Network Management System (NMS)**.

---

## The Three Ways SNMP Operates

SNMP communication occurs in three different modes depending on whether the **Network Management System (Manager)** is collecting information, modifying a device, or receiving notifications.

``` mermaid
flowchart TD

A["SNMP Communication Modes"]

A --> B["1. READ Mode<br/>(Polling / Pull)"]
A --> C["2. WRITE Mode<br/>(Configuration / Push)"]
A --> D["3. NOTIFICATION Mode<br/>(Events / Alerts)"]

B --> B1["GET"]
B --> B2["GETNEXT"]
B --> B3["GETBULK"]

C --> C1["SET"]

D --> D1["TRAP"]
D --> D2["INFORM"]

```

---

# Mode 1: READ (Polling / "Pull")

**Initiated By:** Network Management System (Manager)

**UDP Port:** `161`

This is the NMS routinely asking routers and switches:

> *"How are you doing right now?"*

``` mermaid
sequenceDiagram
    participant NMS as NMS (Manager)
    participant Agent as Managed Switch (Agent)

    NMS->>Agent: GET / GETNEXT / GETBULK (OID Request)
    Note over Agent: Looks up the requested OID<br/>inside the MIB Database
    Agent-->>NMS: RESPONSE (OID Value)
```

### Example

``` text
GET OID 1.3.6.1.2.1.2.2.1.10.1

↓

Matches:

GigabitEthernet0/1 Incoming Traffic

↓

Response:

1,524,980 bytes
```

### Request Types

| Command | Purpose |
| :--- | :--- |
| **GET** | Retrieves one specific value (e.g., CPU utilization or interface status). |
| **GETNEXT** | Retrieves the next object in the MIB tree. Useful for walking through interface tables sequentially. |
| **GETBULK** | Retrieves multiple values in a single request instead of sending numerous GET requests. Introduced in **SNMPv2c**. |

---

# Mode 2: WRITE ("Push")

**Initiated By:** Network Management System (Manager)

**UDP Port:** `161`

SNMP can also modify configurations on managed devices using the **SET** operation.

``` mermaid
sequenceDiagram
    participant NMS as NMS (Manager)
    participant Agent as Managed Router (Agent)

    NMS->>Agent: SET (OID = Value)
    Note over Agent: Looks up the OID<br/>Executes the configuration change
    Agent-->>NMS: RESPONSE (Success)
```

### Example

``` text
SET OID

1.3.6.1.2.1.2.2.1.7.1 = 2

↓

OID corresponds to:

Interface Administrative Status

↓

Action Performed:

Shutdown GigabitEthernet0/1

↓

Response:

Success
```

---

# Mode 3: NOTIFICATIONS ("Push" from Agent)

**Initiated By:** Managed Device (Agent)

**UDP Port:** `162`

Instead of waiting for the NMS to request information, the managed device can proactively notify the manager whenever an important event occurs.

Examples include:

- Power supply failure
- Interface goes down
- Cable unplugged
- High CPU utilization
- Fan failure

---

## Option A: TRAP (Fire and Forget)

``` mermaid
sequenceDiagram
    participant Agent as Managed Device
    participant NMS as Network Management System

    Agent-->>NMS: TRAP
```

### Characteristics

- Uses **UDP Port 162**
- No application-level acknowledgment
- Fast notification
- If the packet is lost, the alert is lost forever

> [!IMPORTANT]
> **TRAP** is considered **unreliable** because the sender never knows whether the manager actually received the notification.

---

## Option B: INFORM (Reliable Alert)

``` mermaid
sequenceDiagram
    participant Agent as Managed Device
    participant NMS as Network Management System

    Agent->>NMS: INFORM
    NMS-->>Agent: ACK
```

### Characteristics

- Uses **UDP Port 162**
- Introduced in **SNMPv2c**
- Requires an acknowledgment (ACK)
- If no ACK is received, the agent retransmits the notification

> [!NOTE]
> **INFORM** was introduced to solve the reliability problem associated with TRAP messages.

---

# SNMP Versions & Security

Understanding the security improvements across SNMP versions is an important CCNA objective.

| Version | Authentication / Encryption | Security Level |
| :--- | :--- | :--- |
| **SNMPv1** | Plaintext Community Strings (Read-Only / Read-Write) | Very Weak (Community strings are transmitted in plain text) |
| **SNMPv2c** | Plaintext Community Strings (Introduced GETBULK & INFORM) | Very Weak (Community strings are transmitted in plain text) |
| **SNMPv3** | User-based Security Model (USM) | Strong (Supports Authentication and Encryption) |

---

## References

| Resource / Document Title | Link |
| :--- | :--- |
| RFC 1157 — Simple Network Management Protocol (SNMPv1) | https://www.rfc-editor.org/rfc/rfc1157 |
| RFC 1905 — Protocol Operations for SNMPv2 | https://www.rfc-editor.org/rfc/rfc1905 |
| RFC 3411 — SNMP Architecture | https://www.rfc-editor.org/rfc/rfc3411 |
| RFC 3414 — User-based Security Model (USM) | https://www.rfc-editor.org/rfc/rfc3414 |
| Cisco SNMP Configuration Guide | https://www.cisco.com/c/en/us/support/docs/ip/simple-network-management-protocol-snmp/ |
| Wikipedia — Simple Network Management Protocol | https://en.wikipedia.org/wiki/Simple_Network_Management_Protocol |