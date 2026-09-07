# Networking Troubleshooting Labs

I made this repo to practice troubleshooting instead of only building labs that already work.

For each scenario, I introduce a problem on purpose, then work through it using the symptoms and Cisco show commands before changing the configuration. The idea is to get better at narrowing down where the problem actually is.

## Cases

- [Wrong access VLAN](./01-wrong-access-vlan/) — one host can't reach its gateway because its switchport is assigned to the wrong VLAN

## My basic troubleshooting process

I don't follow this as a rigid checklist every time, but these are the things I try to think through:

1. What exactly is failing?
2. Is it one device or multiple devices?
3. Is the interface up?
4. Is the IP address, subnet mask, and gateway correct?
5. Is the device in the right VLAN?
6. Are trunks and routes present where they should be?
7. Is an ACL or routing problem blocking the traffic?
8. After making a change, can I prove the problem is actually fixed?

## Commands I use often

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

I'll add more broken-lab scenarios here as I work through different CCNA topics.
