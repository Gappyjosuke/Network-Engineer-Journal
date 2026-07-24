[*← Back to CCNA Index*](../README.MD)

# IPv4 & IPv6 Multicast Addresses

Unlike unicast addresses, **multicast addresses are never assigned to a device interface**. Instead, they identify a **group of devices** interested in receiving the same traffic.

A multicast address behaves much like a **radio station frequency**—only devices "tuned" to that group process the packets, while every other device ignores them.

---

# What is a Multicast Address?

A multicast address is **not** an IP address configured on an interface.

Instead, it serves as a **Group Membership Identifier**.

> [!NOTE]
> Devices join multicast groups based on the protocols they are running. If a device is not interested in a particular multicast group, it simply ignores the traffic.

---

## Example: OSPF Multicast

Suppose an OSPF router wants to send a Hello packet to every neighboring OSPF router on the LAN.

The packet is transmitted using:

| Field | Value |
| :--- | :--- |
| **Destination IP** | `224.0.0.5` |
| **Destination MAC** | `01-00-5E-xx-xx-xx` (IPv4 Multicast MAC Address) |

Every device connected to the switch physically receives the Ethernet frame.

However:

- OSPF routers are listening for **224.0.0.5**
- PCs, printers, and other non-OSPF devices immediately discard the packet at the **Network Interface Card (NIC)** without passing it to the operating system, avoiding unnecessary CPU processing.

---

# Link-Local Multicast Addresses

The following multicast groups are considered **Link-Local**.

These packets:

- Are never forwarded by routers.
- Always remain within the local subnet.
- Typically have a **TTL (IPv4)** or **Hop Limit (IPv6)** of **1**.

| Purpose | IPv4 | IPv6 |
| :--- | :--- | :--- |
| **All Nodes** | `224.0.0.1` | `FF02::1` |
| **All Routers** | `224.0.0.2` | `FF02::2` |
| **All OSPF Routers** | `224.0.0.5` | `FF02::5` |
| **OSPF DR / BDR** | `224.0.0.6` | `FF02::6` |
| **RIPv2 / RIPng Routers** | `224.0.0.9` | `FF02::9` |
| **EIGRP Routers** | `224.0.0.10` | `FF02::A` |

---

# Understanding Each Multicast Address

## 224.0.0.1 / FF02::1 — All Nodes

**Purpose**

Replaces the traditional IPv4 broadcast (`255.255.255.255`) in many IPv6 operations.

**Function**

Used whenever a protocol must communicate with **every active device** on the local network.

Examples include:

- IPv6 Neighbor Discovery
- Local network discovery

---

## 224.0.0.2 / FF02::2 — All Routers

**Purpose**

Allows hosts to communicate with **routers only**, without disturbing end-user devices.

**Function**

Commonly used by router discovery protocols.

Example:

A PC sends an **IPv6 Router Solicitation** asking:

> *"Is there a router on this network that can help me configure my IPv6 address?"*

Only routers process this multicast group.

---

## 224.0.0.5 / FF02::5 — All OSPF Routers

**Purpose**

Used by OSPF-enabled routers.

**Function**

Carries:

- OSPF Hello packets
- Link-State Updates

Every OSPF-enabled interface listens on this multicast address.

---

## 224.0.0.6 / FF02::6 — OSPF DR / BDR

On shared Ethernet networks, OSPF elects:

- Designated Router (DR)
- Backup Designated Router (BDR)

Rather than sending updates to every router individually, non-designated routers (**DROTHERs**) transmit their Link-State Updates only to:

```
224.0.0.6
```

This significantly reduces unnecessary routing traffic.

> [!IMPORTANT]
> Instead of every router flooding updates to every other router, all updates are sent only to the DR and BDR, greatly improving efficiency on multi-access networks.

---

## 224.0.0.9 / FF02::9 — RIPv2 / RIPng

**Purpose**

Used by RIP routers to exchange routing information.

**Function**

RIPv2 periodically sends its routing table every **30 seconds** using multicast.

Earlier versions (RIPv1) used:

```
255.255.255.255
```

which forced every device on the LAN to process the packet.

RIPv2 changed this to:

```
224.0.0.9
```

allowing only RIP routers to receive the updates.

---

## 224.0.0.10 / FF02::A — EIGRP

**Purpose**

Used exclusively by Cisco's Enhanced Interior Gateway Routing Protocol (EIGRP).

**Function**

Carries:

- Hello Packets
- Update Packets
- Query Packets

These messages are used to:

- Discover neighbors
- Maintain adjacencies
- Synchronize routing tables

> [!NOTE]
> In hexadecimal, **10 = A**, which explains why the IPv6 equivalent of `224.0.0.10` is `FF02::A`.

---

# IPv6 Multicast Address Range

The IPv6 multicast range is:

```
FF00::/8
```

The second byte identifies the multicast **Scope**.

| Scope | Meaning |
| :--- | :--- |
| `FF01::/16` | Interface-Local |
| `FF02::/16` | Link-Local (Same LAN Segment) |
| `FF05::/16` | Site-Local |

---

# Summary Sheet

| Topic | Value |
| :--- | :--- |
| **IPv4 Multicast Range** | Class D (`224.0.0.0` – `239.255.255.255`) |
| **IPv6 Multicast Range** | `FF00::/8` |
| **Link-Local IPv6 Scope** | `FF02::/16` |
| **IPv4 OSPF Routers** | `224.0.0.5` |
| **IPv4 OSPF DR / BDR** | `224.0.0.6` |
| **IPv4 RIP Routers** | `224.0.0.9` |
| **IPv4 EIGRP Routers** | `224.0.0.10` |
| **Hexadecimal Tip** | Decimal **10** = Hexadecimal **A**, therefore `224.0.0.10` corresponds to `FF02::A` in IPv6. |

---

## References

| Resource / Document Title | Link |
| :--- | :--- |
| RFC 1112 — Host Extensions for IP Multicasting | https://www.rfc-editor.org/rfc/rfc1112 |
| RFC 4291 — IPv6 Addressing Architecture | https://www.rfc-editor.org/rfc/rfc4291 |
| RFC 2328 — OSPF Version 2 | https://www.rfc-editor.org/rfc/rfc2328 |
| RFC 5340 — OSPF for IPv6 | https://www.rfc-editor.org/rfc/rfc5340 |
| RFC 2453 — RIP Version 2 | https://www.rfc-editor.org/rfc/rfc2453 |
| RFC 2080 — RIPng for IPv6 | https://www.rfc-editor.org/rfc/rfc2080 |
| Cisco OSPF Configuration Guide | https://www.cisco.com/c/en/us/support/ip/open-shortest-path-first-ospf/products-installation-and-configuration-guides-list.html |
| Cisco IP Multicast Configuration Guide | https://www.cisco.com/c/en/us/support/ip/ip-multicast/products-installation-and-configuration-guides-list.html |
| Wikipedia — IP Multicast | https://en.wikipedia.org/wiki/IP_multicast |
