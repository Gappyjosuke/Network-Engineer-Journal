[*← Back to CCNA Index*](../README.md)

# DHCP: The DORA Process & Mechanics

DHCP (**Dynamic Host Configuration Protocol**) gives end hosts everything they need to communicate on an IP network: an **IP address**, **Subnet Mask**, **Default Gateway**, and **DNS Server**.

---

## The Complete DORA Flow

When a host boots without an IP address, it goes through the **DORA** sequence:

``` mermaid
sequenceDiagram
    participant PC as Client (PC)
    participant DHCP as DHCP Server

    PC->>DHCP: 1. DISCOVER (Broadcast: 0.0.0.0 → 255.255.255.255)
    DHCP-->>PC: 2. OFFER (Unicast / Broadcast)
    PC->>DHCP: 3. REQUEST (Broadcast: 0.0.0.0 → 255.255.255.255)
    DHCP-->>PC: 4. ACK (Unicast / Broadcast)
```

---

## Step-by-Step Breakdown

### Step 1: DISCOVER (Client → Server)

**Goal:** The client shouts to find any available DHCP server:

> *"Is there a DHCP server out there? I need an IP address!"*

| Header Field | Value | Notes |
| :--- | :--- | :--- |
| **Source IP** | `0.0.0.0` | Client has no IP address yet |
| **Destination IP** | `255.255.255.255` | Layer 3 Broadcast |
| **Source MAC** | Client's MAC Address | Unique hardware address |
| **Destination MAC** | `FF:FF:FF:FF:FF:FF` | Layer 2 Broadcast |

---

### Step 2: OFFER (Server → Client)

**Goal:** The server responds with a proposed lease:

> *"Here is an IP address you can use: `192.168.1.100`."*

**Included Parameters:**
* Proposed IP Address
* Subnet Mask
* Default Gateway
* DNS Server IP(s)
* Lease Duration

#### BOOTP Broadcast Flag Mechanics

| Broadcast Bit | Server Action |
| :---: | :--- |
| **`1`** | Sends the Offer as a **Broadcast** (`255.255.255.255`) |
| **`0`** | Sends the Offer as a **Unicast** frame directed to the client's MAC address |

---

### Step 3: REQUEST (Client → Server)

**Goal:** The client formally accepts the offer:

> *"I accept the offer for `192.168.1.100` from Server `192.168.1.1`!"*

> **Why is this STILL a Broadcast?**  
> If multiple DHCP servers offered IP addresses, sending this Request as a broadcast politely notifies all other servers: *"Thanks for the offer, but I chose a different server. You can reclaim your offered IP addresses now."*

---

### Step 4: ACK / ACKNOWLEDGMENT (Server → Client)

**Goal:** The chosen server finalizes the lease:

> *"Got it! `192.168.1.100` is officially leased to you for the next 24 hours."*

Once the client receives the ACK, it binds the IP to its interface and runs **Duplicate Address Detection (DAD / Gratuitous ARP)** to verify that no other host is actively using `192.168.1.100`.

---

## Releasing & Renewing Leases

* **`ipconfig /release`**  
  Client sends a **Unicast DHCP RELEASE** message directly to the server, surrendering its IP back to the pool.
* **`ipconfig /renew`**  
  Client sends a **Unicast DHCP REQUEST** straight to its original DHCP server at **50% of the lease time (T1 timer)** to extend its lease.

---

## DHCP Relay Agent (`ip helper-address`)

Because **DISCOVER** messages are **Layer 3 Broadcasts (`255.255.255.255`)**, routers drop them by default. If your DHCP server lives on a **different subnet**, the Discover packet will never reach it without a relay.

``` mermaid
flowchart LR
    A[PC] -->|1. Broadcast Discover| B[Router / Relay Agent]
    B -->|2. Unicast Discover| C[DHCP Server]
```

### Operational Steps:
1. Configure `ip helper-address <DHCP-Server-IP>` on the client-facing interface (e.g., `g0/0`).
2. When the router receives a broadcast DHCP Discover on `g0/0`, it intercepts it.
3. The router converts the broadcast into a **Unicast IP packet** addressed directly to the DHCP server.
4. The router routes it across subnets to the destination server.

>[!NOTE]
> **Bonus Fact:** The `ip helper-address` command forwards **8 common UDP broadcast services** by default:
> * DHCP / BOOTP (Ports 67/68)
> * DNS (Port 53)
> * TFTP (Port 69)
> * TACACS (Port 49)
> * Time (Port 37)
> * IEN116 Name Service (Port 42)
> * NetBIOS Name Service (Port 137)
> * NetBIOS Datagram Service (Port 138)

---

## Cisco IOS DHCP Server Configuration

``` cisco
! 1. Exclude static IPs first (e.g., Gateways, Servers, Printers)
Router(config)# ip dhcp excluded-address 192.168.1.1 192.168.1.10

! 2. Create the Pool & Define Parameters
Router(config)# ip dhcp pool LAN_POOL
Router(dhcp-config)# network 192.168.1.0 255.255.255.0
Router(dhcp-config)# default-router 192.168.1.1
Router(dhcp-config)# dns-server 8.8.8.8
Router(dhcp-config)# domain-name mylab.local
Router(dhcp-config)# lease 1 12 0

! 3. Verification Commands
Router# show ip dhcp binding
Router# show ip dhcp pool
Router# show ip dhcp server statistics

```
---

| Resource / Document Title | Link |
| :--- | :--- |
| RFC 2131 — Dynamic Host Configuration Protocol | [rfc-editor.org/rfc2131](https://www.rfc-editor.org/rfc/rfc2131) |
| RFC 2132 — DHCP Options and BOOTP Vendor Extensions | [rfc-editor.org/rfc2132](https://www.rfc-editor.org/rfc/rfc2132) |
| Cisco IOS IP Addressing Services Configuration Guide | [cisco.com Guide](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/ipaddr_dhcp/configuration/15-e/dhcp-15-e-book.html) |
| Troubleshooting DHCP in Enterprise Networks | [cisco.com Tech Note](https://www.cisco.com/c/en/us/support/docs/ip/dynamic-host-configuration-protocol-dhcp/) |
| Wikipedia — Dynamic Host Configuration Protocol | [en.wikipedia.org/DHCP](https://en.wikipedia.org/wiki/Dynamic_Host_Configuration_Protocol) |
| Wikipedia — DHCP Relay Agent | [en.wikipedia.org/DHCP_relay](https://en.wikipedia.org/wiki/DHCP_relay) |