# Troubleshooting Lab 01 — Wrong Access VLAN

For this lab I intentionally put PC2 in the wrong VLAN, then worked through the problem from the symptoms instead of immediately looking at the config.

## Expected setup

- PC2: `192.168.20.10/24`
- Default gateway: `192.168.20.1`
- PC2 switchport: VLAN 20

## Symptom

PC2 could not ping its gateway:

```text
PC2> ping 192.168.20.1
Request timed out.
```

Other devices in VLAN 20 were working, so I knew the problem was probably local to PC2 or its switchport.

## What I checked

First I confirmed the IP address, subnet mask, and default gateway on PC2.

Then I checked the switch port:

```text
show interfaces status
show vlan brief
```

The interface was up, but `show vlan brief` showed the PC2 port under VLAN 10 instead of VLAN 20.

I confirmed it with:

```text
show interfaces fa0/2 switchport
```

The relevant line showed:

```text
Access Mode VLAN: 10
```

## Fix

```text
configure terminal
interface fa0/2
 switchport mode access
 switchport access vlan 20
end
```

## Verification

I checked `show vlan brief` again to make sure Fa0/2 moved to VLAN 20, then retried the ping to `192.168.20.1`.

## What I took away from it

The host had the right Layer 3 settings, but its switchport put the traffic into the wrong Layer 2 VLAN. Because the problem only affected one host, checking that host and its access port first was faster than changing anything on the router.

For a similar issue, my first checks would be:

1. Link/interface state
2. Host IP settings
3. Access VLAN
4. MAC address learning
5. Default-gateway reachability
