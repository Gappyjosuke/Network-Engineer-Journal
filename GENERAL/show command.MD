[*← Back to CCNA Index*](../README.md)

# A Guide to Cisco `show` Commands & Modifiers

It's completely normal to get lost in **show** commands. Use this guide to quickly find the exact command or CLI filter you need.

---

## Which `show` Command Do I Need?

``` mermaid
flowchart TD
    A["Which show command do I need?"]
    A --> B["Quick Overview<br/>(Status, IP, Up/Down)"]
    A --> C["Detailed Specs<br/>(MAC, MTU, Counters)"]
    A --> D["Specific Protocol<br/>(Routing, DHCP, OSPF)"]
    
    B --> B1["show ip interface brief"]
    C --> C1["show interface g0/1"]
    D --> D1["show ip route"]
    D --> D2["show ip dhcp binding"]
``` 

---

## Summary of Essential `show` Commands

| Command | What It Tells You | Best Used For... |
| :--- | :--- | :--- |
| **`show ip interface brief`** | Summary table of all interfaces, assigned IPs, and Status (Up/Up, Admin Down). | Quick 5-second health check of all ports. |
| **`show interface <int>`** | Full details: MAC address, MTU, Speed/Duplex, Input/Output errors, CRC errors. | Physical layer troubleshooting (e.g., bad cable, duplex mismatch). |
| **`show ip interface <int>`** | Layer 3 specifics: Inbound/Outbound ACLs applied, Helper addresses, Proxy ARP state. | Troubleshooting IP/Security feature misconfigurations on a port. |
| **`show ip route`** | The entire Layer 3 Routing Table (Connected, Static, OSPF, EIGRP). | Checking if the router knows how to reach a destination network. |
| **`show running-config`** | The complete active system configuration stored in RAM. | Reviewing exact syntax typed under global or interface modes. |

---

# Mastering CLI Output Piping (`|`)

Piping takes the long output of a **show** command and filters it so you only see what you care about.

``` cisco
Router# show running-config | <modifier> <keyword>
``` 

---

## How Piping Modifiers Filter Text

### Example Input Text

``` cisco
interface GigabitEthernet0/0
 ip address 192.168.1.1 255.255.255.0
 duplex auto
!
interface GigabitEthernet0/1
 ip address 10.0.0.1 255.255.255.0
 shutdown
```

### Modifiers in Action

#### 1. `| include <keyword>`
Shows **ONLY** lines containing the specified keyword.

* **Example:** `show running-config | include ip address`
``` cisco
 ip address 192.168.1.1 255.255.255.0
 ip address 10.0.0.1 255.255.255.0
```

#### 2. `| exclude <keyword>`
Shows everything **EXCEPT** lines containing the specified keyword.

* **Example:** `show ip interface brief | exclude unassigned`

#### 3. `| section <keyword>`
Shows the **ENTIRE configuration block** matching the specified keyword.

* **Example:** `show running-config | section GigabitEthernet0/1`
``` cisco
interface GigabitEthernet0/1
 ip address 10.0.0.1 255.255.255.0
 shutdown
```
#### 4. `| begin <keyword>`
Jumps directly to where the keyword first appears and prints everything from that point onward.

* **Example:** `show running-config | begin router ospf`

---

## Practical Exam & Field Examples

* **Find all active/configured IP addresses fast:**
  ``` cisco
  show ip interface brief | exclude unassigned
  ```

* **Check OSPF configuration block quickly:**
  ``` cisco
  show running-config | section router ospf
  ```

* **Check configuration for Interface `g0/0` only:**
  ``` cisco
  show running-config | section GigabitEthernet0/0
  ```

---

| Resource / Document Title | Link |
| :--- | :--- |
| Cisco IOS Command Reference List |[Cisco IOS](https://www.cisco.com/c/en/us/support/ios-nx-os-software/ios-software/products-command-reference-list.html) |
| Cisco IOS Fundamentals Command Reference Guide | [Cisco IOS Command Reference](https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/fundamentals/command/reference/cf_book.html) |

