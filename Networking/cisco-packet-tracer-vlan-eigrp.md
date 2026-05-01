## NETWORK ENGINEERING

## Scenario
During a routine security review at a Nigeria fintech company that has headquarters on Victoria Island and a branch office in Ikoyi with 2 departments each (IT and HR) and 3 workers per department, the SOC team raised a red flag — the company's internal network had zero segmentation. Every device across both office locations sat on the same flat network, meaning:
-  A compromised HR machine could pivot directly into IT infrastructure with no network barrier
-  An unauthorised device could plug into any switch port and immediately access the entire network
- The HQ and branch office had no internal network  connection — staff were sharing sensitive files over personal messaging apps
- HR payroll data was accessible to IT staff on the same flat network — a compliance violation

As a network engineer, you are expected to redesign the network topology that resolves the issues.

## Objective
- Separate the HQ and branch into 2 separate LANS
- segmenting departments using VLANs
- establishing secure inter-site connectivity over a dedicated WAN link ((45.0.1.0/30)
- enforcing access control policies using ACLs and port security.

# Solution
## Network Architecture
- 2 LANs (each with VLANs 10 & 20)|2 Routers (Cisco 4330) |2 Switches (2960) |Routers connected via WAN (45.0.1.0/30)
- HQ- Victoria Island (LAN1): VLAN10 IT → 192.168.1.0/24 | VLAN20 HR → 192.168.2.0/24
- Branch- Ikoyi (LAN2): VLAN10 IT → 192.168.3.0/24 | VLAN20 HR → 192.168.4.0/24  
- WAN: 45.0.1.0/30 | R1 → 45.0.1.1 | R2 → 45.0.1.2
- 3 computers across each department | 12 computers in total.


##  Configurations

### 🧱 STEP 1: TOPOLOGY
Devices:

* 2 × Router 4330 (R1, R2)
* 2 × Switch 2960 (S1, S2)
* 6 PCs per LAN (total 12)

<img width="1133" height="421" alt="image" src="https://github.com/user-attachments/assets/f8920107-e221-4293-940e-1482e5669e0f" />

### Connections:

* PCs → Switch ---> straight through cable
* Switch → Router (trunk)
* Router ↔ Router (serial or gig link)

---

# 🌐 IP PLAN

### LAN 1 (HQ)

* VLAN 10 → 192.168.1.0/24
* VLAN 20 → 192.168.2.0/24

### LAN 2 (IKOYI)

* VLAN 10 → 192.168.3.0/24
* VLAN 20 → 192.168.4.0/24

### WAN

* R1 → 45.0.1.1/30
* R2 → 45.0.1.2/30

---

# 🔧 STEP 2: SWITCH CONFIG (S1 & S2)

### Create VLANs for both switches

```
enable
conf t
vlan 10
name IT
vlan 20
name HR
```

### Assign Ports

(Example S1)

```
interface range fa0/1-3
switchport mode access
switchport access vlan 10

interface range fa0/4-6
switchport mode access
switchport access vlan 20

```

<img width="637" height="473" alt="Screenshot 2026-04-30 125259" src="https://github.com/user-attachments/assets/3458fa5d-0079-4ce2-bb5b-555514f47f80" />


### Trunk to Router

```
interface fa0/24
switchport mode trunk
```

---

# 🔁 STEP 3: ROUTER CONFIG (ROUTER-ON-A-STICK)

## R1 CONFIG

```
enable
conf t

interface g0/0
no shut

interface g0/0.10
encapsulation dot1Q 10
ip address 192.168.1.1 255.255.255.0

interface g0/0.20
encapsulation dot1Q 20
ip address 192.168.2.1 255.255.255.0
```

## R2 CONFIG

```
interface g0/0/0
no shut

interface g0/0/0.10
encapsulation dot1Q 10
ip address 192.168.3.1 255.255.255.0

interface g0/0/0.20
encapsulation dot1Q 20
ip address 192.168.4.1 255.255.255.0
```
<img width="478" height="90" alt="Screenshot 2026-04-30 133946" src="https://github.com/user-attachments/assets/47603f06-458b-4d0c-8a01-d5dad6c51f74" />

---

# 🔗 STEP 4: ROUTER TO ROUTER LINK

## R1

```
interface g0/0/1
ip address 45.0.1.1 255.255.255.252
no shut
```

## R2

```
interface g0/0/1
ip address 45.0.1.2 255.255.255.252
no shut
```

---

# 📍 STEP 5: STATIC ROUTING

## R1

```
ip route 192.168.3.0 255.255.255.0 45.0.1.2
ip route 192.168.4.0 255.255.255.0 45.0.1.2
```

## R2

```
ip route 192.168.1.0 255.255.255.0 45.0.1.1
ip route 192.168.2.0 255.255.255.0 45.0.1.1
```

# 🧪 STEP 6: TEST

From any PC:

```
ping 192.168.3.x
ping 192.168.4.x
```

---

# ❌ STEP 7: REMOVE STATIC ROUTES

```
no ip route 192.168.3.0 255.255.255.0 45.0.1.2
no ip route 192.168.4.0 255.255.255.0 45.0.1.2
```

(Same on R2)

---

# 🔄 STEP 8: CONFIGURE EIGRP

## R1

```
router eigrp 100
network 192.168.1.0
network 192.168.2.0
network 45.0.1.0
no auto-summary
```

## R2

```
router eigrp 100
network 192.168.3.0
network 192.168.4.0
network 45.0.1.0
no auto-summary
```

👉 Dynamic routing automatically learns routes instead of manual configuration 

---

# 🔒 STEP 9: PORT SECURITY (BLOCK HOST)

### Example: Block PC in VLAN 10 (192.168.1.10)

On S1:

```
interface fa0/1
switchport mode access
switchport access vlan 10

switchport port-security
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation shutdown
```

<img width="475" height="183" alt="Screenshot 2026-04-30 205805" src="https://github.com/user-attachments/assets/fd34e40f-a599-4cb0-b62d-602fa9069b5a" />



👉 To block access specifically to VLAN 20, use ACL on router:

```
access-list 100 deny ip host 192.168.1.10 192.168.2.0 0.0.0.255
access-list 100 permit ip any any

interface g0/0.10
ip access-group 100 in
```

