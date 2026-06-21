# CCNA-Day-07-STP-Root-Bridge
# CCNA Lab Day 7 – STP Root Bridge Control, PortFast, and BPDU Guard

## Overview

This lab builds on the Spanning Tree Protocol concepts learned in Day 6. Instead of allowing STP to automatically select the Root Bridge, this lab focuses on manually controlling the Root Bridge election and implementing STP security features such as PortFast and BPDU Guard.

These features are widely used in enterprise networks to improve performance and prevent accidental Layer 2 loops.

---

## Objectives

After completing this lab, you will be able to:

- Configure a Primary Root Bridge
- Configure a Secondary Root Bridge
- Understand Root Bridge control
- Configure PortFast
- Configure BPDU Guard
- Verify STP operation
- Troubleshoot STP-related issues
- Implement STP security best practices

---

## Network Topology

```text
                    SW1
                   /   \
                  /     \
                 /       \
               SW2-------SW3

                |         |
               PC1       PC2
```

---

## Devices Used

- 3 Cisco Switches
- 2 PCs
- Copper Straight-Through Cables

---

## Physical Connections

| Device | Interface | Connected To |
|----------|----------|----------|
| SW1 Fa0/1 | SW2 Fa0/1 |
| SW1 Fa0/2 | SW3 Fa0/1 |
| SW2 Fa0/2 | SW3 Fa0/2 |
| PC1 Fa0 | SW2 Fa0/10 |
| PC2 Fa0 | SW3 Fa0/10 |

---

## IP Addressing

### VLAN 10

| Device | IP Address |
|----------|----------|
| PC1 | 192.168.10.10 |
| PC2 | 192.168.10.20 |

Subnet Mask:

```text
255.255.255.0
```

---

## Step 1: Create VLAN 10

Configure on all switches:

```bash
enable
configure terminal

vlan 10
name USERS
```

---

## Step 2: Configure Access Ports

### SW2

```bash
interface fa0/10
switchport mode access
switchport access vlan 10
```

### SW3

```bash
interface fa0/10
switchport mode access
switchport access vlan 10
```

---

## Step 3: Configure Trunk Links

### SW1

```bash
interface range fa0/1 - 2
switchport mode trunk
```

### SW2

```bash
interface range fa0/1 - 2
switchport mode trunk
```

### SW3

```bash
interface range fa0/1 - 2
switchport mode trunk
```

---

## Root Bridge Control

In enterprise networks, engineers manually select the Root Bridge to control traffic flow.

### Configure SW1 as Primary Root Bridge

```bash
configure terminal

spanning-tree vlan 10 root primary
```

Verify:

```bash
show spanning-tree
```

Expected Output:

```text
This bridge is the root
```

---

## Configure SW2 as Secondary Root Bridge

```bash
configure terminal

spanning-tree vlan 10 root secondary
```

Purpose:

- SW1 acts as the Primary Root Bridge
- SW2 acts as the Backup Root Bridge

If SW1 fails, SW2 automatically becomes the Root Bridge.

---

## Verify Root Bridge Information

```bash
show spanning-tree root
```

Verify:

- Root Bridge ID
- Root Port
- Root Path Cost

---

## Understanding PortFast

Normally, STP ports move through several states before forwarding traffic.

```text
Blocking
   ↓
Listening
   ↓
Learning
   ↓
Forwarding
```

This process can take approximately 30 seconds.

For end devices such as PCs, this delay is unnecessary.

PortFast allows ports to immediately enter the Forwarding state.

---

## Configure PortFast

### SW2

```bash
interface fa0/10
spanning-tree portfast
```

### SW3

```bash
interface fa0/10
spanning-tree portfast
```

---

## Verify PortFast

```bash
show spanning-tree interface fa0/10 detail
```

---

## PortFast Best Practice

Use PortFast on:

- PCs
- Printers
- Servers
- End-user devices

Do Not Use PortFast on:

- Switch-to-Switch Links
- Trunk Links
- Network Infrastructure Links

---

## Understanding BPDU Guard

PortFast improves startup speed but introduces a security risk.

If someone connects a switch to a PortFast-enabled port, it could create a Layer 2 loop.

BPDU Guard protects against this.

---

## Configure BPDU Guard

### SW2

```bash
interface fa0/10
spanning-tree bpduguard enable
```

### SW3

```bash
interface fa0/10
spanning-tree bpduguard enable
```

---

## How BPDU Guard Works

If a BPDU is received on a PortFast-enabled port:

```text
BPDU Received
      ↓
Port Shutdown
```

The port enters an Err-Disabled state to protect the network.

---

## Verification Commands

### Verify STP

```bash
show spanning-tree
```

### Verify Root Bridge

```bash
show spanning-tree root
```

### Verify PortFast

```bash
show spanning-tree interface fa0/10 detail
```

### Verify BPDU Guard

```bash
show running-config interface fa0/10
```

### Check Err-Disabled Ports

```bash
show interfaces status
```

---

## Connectivity Testing

### Test PC-to-PC Communication

From PC1:

```bash
ping 192.168.10.20
```

Expected Result:

Success

---

## Troubleshooting Scenario 1

Remove Root Bridge Configuration:

```bash
no spanning-tree vlan 10 root primary
```

Verify:

```bash
show spanning-tree
```

Observe whether the Root Bridge changes.

---

## Troubleshooting Scenario 2

Connect a Switch to a PortFast Port

Result:

```text
BPDU Received
      ↓
Err-Disabled State
```

Verify:

```bash
show interfaces status
```

---

## Recover an Err-Disabled Port

```bash
interface fa0/10

shutdown

no shutdown
```

---

## Mini Challenge

### Topology

```text
              SW1
            /     \
          SW2-----SW3

          |        |
         PC1      PC2
```

### Tasks

1. Create VLAN 10
2. Configure trunk links
3. Configure SW1 as Root Primary
4. Configure SW2 as Root Secondary
5. Configure PortFast on access ports
6. Configure BPDU Guard on access ports
7. Verify all settings

---

## Skills Learned

- Root Bridge Control
- Primary Root Configuration
- Secondary Root Configuration
- PortFast Configuration
- BPDU Guard Configuration
- STP Security
- STP Verification
- STP Troubleshooting

---

## Conclusion

This lab demonstrated how network engineers control and secure Spanning Tree Protocol in enterprise networks. By configuring Root Bridge priorities, PortFast, and BPDU Guard, the network becomes more predictable, secure, and efficient.

---

## Author

Muhammad Kausar

CCNA Enterprise Networking Lab Series
