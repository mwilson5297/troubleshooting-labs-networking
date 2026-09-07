# Troubleshooting Case 01 — Host in the Wrong Access VLAN

## Incident Summary

A user reports that PC2 cannot reach its default gateway or communicate with devices in VLAN 20. Other VLAN 20 hosts are working normally.

## Expected State

- PC2: `192.168.20.10/24`
- Default gateway: `192.168.20.1`
- PC2 switchport: access VLAN 20
- VLAN 20 is active on the switch

## Symptoms

```text
PC2> ping 192.168.20.1
Request timed out.
```

The failure affects one host rather than the entire VLAN, so the initial scope suggests a host-specific or access-layer problem.

## Investigation

### 1. Verify host addressing

PC2 has the expected IP address, mask, and gateway.

### 2. Check interface state

```text
show interfaces status
```

The connected switchport is physically up.

### 3. Check VLAN membership

```text
show vlan brief
```

Observed result:

```text
10   USERS     active    Fa0/2
20   SERVERS   active
```

PC2 is connected to `Fa0/2`, but the port appears under VLAN 10 rather than VLAN 20.

### 4. Confirm switchport configuration

```text
show interfaces fa0/2 switchport
```

Relevant output indicates:

```text
Administrative Mode: static access
Access Mode VLAN: 10
```

## Root Cause

The PC2 access port was assigned to VLAN 10 instead of VLAN 20.

## Remediation

```text
configure terminal
interface fa0/2
 switchport mode access
 switchport access vlan 20
end
```

## Validation

```text
show vlan brief
```

PC2's port should now appear under VLAN 20.

Then retest:

```text
PC2> ping 192.168.20.1
```

The ping should succeed.

## Why the Failure Occurred

An access port places untagged host traffic into its configured VLAN. Even though PC2 had a valid `192.168.20.0/24` IP configuration, the switch was placing its Ethernet frames into VLAN 10. That separated PC2 at Layer 2 from the VLAN 20 gateway.

The host's Layer-3 configuration and the switch's Layer-2 segmentation therefore disagreed.

## Operational Lesson

When a single host cannot reach its gateway but other hosts in the same subnet can, check the local path before changing routing. Verify:

1. Physical interface state
2. Host addressing
3. Access VLAN
4. MAC learning
5. Gateway reachability

This narrows the fault domain and avoids unnecessary changes elsewhere in the network.
