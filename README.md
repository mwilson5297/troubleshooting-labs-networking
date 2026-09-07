# Network Troubleshooting Labs

A portfolio of structured network fault-isolation scenarios designed to demonstrate a practical troubleshooting process rather than command memorization.

## Troubleshooting Framework

For each scenario:

1. **Define the symptom** — What is failing from the user's perspective?
2. **Establish scope** — One host, one VLAN, one subnet, or the entire network?
3. **Check the lowest relevant layer first** — Interface state, VLAN membership, trunking, addressing.
4. **Inspect forwarding and control-plane state** — ARP, MAC table, routes, OSPF neighbors, ACLs.
5. **Form a hypothesis** — Use evidence rather than random configuration changes.
6. **Apply the smallest corrective change**.
7. **Validate end-to-end behavior**.
8. **Document the root cause and prevention lesson**.

## Scenario Roadmap

| Scenario | Likely Fault Domain | Key Verification |
|---|---|---|
| Host cannot reach default gateway | Layer 1/2, VLAN, IP config | `show interfaces`, `show vlan brief`, host addressing |
| Same-VLAN hosts cannot communicate | Access VLAN / port state | `show vlan brief`, MAC table |
| Inter-VLAN routing fails | Trunk/subinterface/gateway | `show interfaces trunk`, `show ip interface brief` |
| OSPF route missing | Adjacency/network statement/cost | `show ip ospf neighbor`, `show ip route ospf` |
| One subnet blocked unexpectedly | ACL logic/placement | `show access-lists`, interface config |
| Redundant gateway not behaving | HSRP state/priority | `show standby brief` |
| DHCP clients fail to obtain addresses | Pool/relay/connectivity | DHCP config, interface addressing |
| STP blocks unexpected path | Root election / topology | `show spanning-tree` |

## Example Case Format

```text
Scenario: PC1 cannot reach Server1
Expected: ICMP succeeds between VLAN 10 and VLAN 20
Symptom: PC1 can ping its gateway but not Server1

Evidence:
- PC1 addressing is correct
- VLAN 10 gateway is reachable
- Router has connected routes for both VLANs
- Server1 cannot ping its own gateway

Hypothesis:
The failure is local to VLAN 20 rather than inter-VLAN routing.

Root Cause:
Server1 switchport assigned to the wrong VLAN.

Fix:
Assign the access port to VLAN 20.

Validation:
- Server1 reaches its gateway
- PC1 reaches Server1
- MAC address appears under VLAN 20
```

## Useful Cisco IOS Commands

```text
show interfaces status
show interfaces switchport
show vlan brief
show interfaces trunk
show mac address-table
show ip interface brief
show arp
show ip route
show ip ospf neighbor
show spanning-tree
show access-lists
show standby brief
ping
traceroute
```

## Goal

The purpose of this repository is to demonstrate the reasoning expected in NOC and network-support work: isolate the fault domain, interpret device state, avoid unnecessary changes, restore service, and clearly explain what failed and why.
