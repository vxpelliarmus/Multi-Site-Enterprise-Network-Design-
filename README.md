# Multi-Site Enterprise Network Design  – Cisco Packet Tracer

A simulated enterprise network connecting a **Headquarters (HQ)** and **two branch offices (BR1, BR2)** through a **dual-router Service Provider (SP) network**. Each site uses a hierarchical LAN design (core + access layers) with department VLANs, DHCP-assigned hosts, and redundant links.

> **File:** `topology.pkt` (open with Cisco Packet Tracer)

---

## Topology Overview

| Site | Role | Key Devices |
|------|------|-------------|
| **Company_HQ_Building** | Headquarters | 1 × 2911 router, 1 × multilayer core switch, 3 × 2960 floor switches, 1 × 2960 management-room switch, 1 × server, PCs |
| **Company_BR1_Building** | Branch office 1 | 1 × 2911 router, 1 × multilayer core switch, 3 × 2960 floor switches, PCs |
| **Company_BR2_Building** | Branch office 2 | 1 × 2911 router, 1 × multilayer core switch, 3 × 2960 floor switches, PCs |
| **Service_Provider (SP)_Building** | ISP / WAN backbone | 2 × 2911 routers (Router_SP1, Router_SP2) |

```
                 ┌─────────────── Service Provider ───────────────┐
                 │        Router_SP2 ◄────────► Router_SP1         │
                 └───────▲──────────────▲──────────────▲───────────┘
                         │ WAN links    │              │
                   Router_BR1      Router_HQ      Router_BR2
                         │              │              │
                  BR1 Core Switch  HQ Core Switch  BR2 Core Switch
                    │    │    │      │    │    │      │    │    │
                   F3   F2   F1     F3   F2   F1     F3   F2   F1
                                         │
                                 Management Room (Server)
```

---

## Features

### 1. Hierarchical LAN design (at every site)
- **Core layer:** one multilayer (Layer 3) switch per site.
- **Access layer:** one 2960 switch per floor (1st, 2nd, 3rd), with end-user PCs attached.
- Clear naming convention for every device (e.g. `Switch0_HQ_2nd_Floor`, `Router_BR1`, `MultiLayer_Switch_BR2_Core_Switch`), which makes the network easy to read and troubleshoot.

### 2. Redundant switching links
- Each floor switch connects to the core switch over **multiple parallel links**, and floor switches are also interconnected with each other.
- Provides **link redundancy and higher bandwidth** between access and core layers (link aggregation / spanning tree prevents loops).

### 3. VLAN segmentation
- Users are separated into department VLANs (e.g. **Admin** and **Employee** VLANs), colour-coded in the diagram.
- VLANs span multiple floors via trunk links, so a department's users can sit on any floor while staying in the same logical network.
- Reduces broadcast traffic and improves security through logical separation.

### 4. Inter-VLAN routing
- The multilayer core switches route between VLANs locally, so inter-department traffic does not need to go out to the WAN router.

### 5. Automatic IP addressing with DHCP
- All user PCs receive addresses **dynamically by DHCP** (labelled "by DHCP" in the topology).
- Servers use **static** addressing (the management server is `192.168.40.254/24`).

### 6. Dedicated network-management segment (HQ)
- A separate **Network Management Room** at HQ with its own switch, a server (`Server0_MR`) and management PCs on `192.168.40.0/24`.

### 7. Multi-site WAN connectivity through a Service Provider
- HQ, BR1 and BR2 each connect to the SP network using their edge routers (Cisco 2911).
- Every site router links to **both SP routers** (SP1 and SP2), and the SP routers are linked to each other, giving **redundant WAN paths** so that a single link failure does not isolate a site.
- Allows branch-to-HQ and branch-to-branch communication across the SP cloud.

---

## IP Addressing Plan

| Site | Segment | Subnet | Assignment |
|------|---------|--------|------------|
| HQ | 1st-floor / Admin users | `192.168.1.0/24` | DHCP |
| HQ | Employee users | `192.168.2.0/24` | DHCP |
| HQ | Management room | `192.168.40.0/24` | DHCP (clients), static (`192.168.40.254` server) |
| BR1 | VLAN 10 – Admin | `192.168.11.0/24` | DHCP |
| BR1 | VLAN 20 – Employee | `192.168.12.0/24` | DHCP |
| BR2 | 1st floor | `192.168.21.0/24` | DHCP |
| BR2 | 2nd & 3rd floor | `192.168.22.0/24` | DHCP |

---

## Device Naming Reference

| Type | Model | Examples |
|------|-------|----------|
| Edge / SP routers | Cisco 2911 | `Router_HQ`, `Router_BR1`, `Router_BR2`, `Router_SP1`, `Router_SP2` |
| Core switches | Multilayer (Layer 3) | `MultiLayer_Switch_HQ_Core_Switch`, `..._BR1_...`, `..._BR2_...` |
| Access switches | Cisco 2960-24TT | `Switch0_HQ_1st_Floor`, `Switch0_BR1_3rd_Floor`, `Switch0_BR2_2nd_Floor` |
| Management switch | Cisco 2960-24TT | `Switch0_Management_Room` |
| Server | Server-PT | `Server0_MR` |
| End devices | PC-PT | `PC0_F1`, `(BR1)PC2_F3`, `(BR2)PC1_F2`, ... |

---

## How to Run / Test

1. Install **Cisco Packet Tracer** and open `topology.pkt`.
2. Wait for the links to turn green (spanning tree convergence can take about 30 seconds). Fast-forward the simulation clock if needed.
3. Open any PC → **Desktop → IP Configuration** and confirm it received an address by DHCP.
4. Test connectivity from a PC → **Desktop → Command Prompt**:
   - Same VLAN / same site: `ping <PC on another floor>`
   - Inter-VLAN: `ping <PC in the other department VLAN>`
   - Across sites: `ping <PC at BR1 / BR2 / HQ>`
   - Management server: `ping 192.168.40.254`
5. Use **Simulation mode** to watch packets travel across floors, the core switch, the edge router and the SP network.
6. Redundancy test: disconnect one of the parallel switch links or one WAN link, then ping again and confirm connectivity continues.

---


## Author

VEXPELLIARMUS 
