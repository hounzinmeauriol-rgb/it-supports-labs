
# Network Troubleshooting Labs

This repository documents my practical networking exercises while learning the fundamentals required for IT Support / First-Level Support.

The labs were mainly completed with Cisco Packet Tracer and Windows networking tools.

## Skills Practiced

- IPv4 addressing
- Subnet masks
- LAN communication
- Ethernet switches
- MAC addresses and MAC address tables
- Routers and default gateways
- Communication between different networks
- DHCP basics
- DNS basics
- Basic network troubleshooting
- Windows network configuration

## Lab Topology

I created two separate local networks in Cisco Packet Tracer.

### LAN 1

- PC0: `192.168.10.10/24`
- PC1: `192.168.10.20/24`
- Default Gateway: `192.168.10.1`
- Switch0

### LAN 2

- PC2: `192.168.20.10/24`
- PC3: `192.168.20.20/24`
- Default Gateway: `192.168.20.1`
- Switch1

### Router

- Interface connected to LAN 1: `192.168.10.1/24`
- Interface connected to LAN 2: `192.168.20.1/24`

The router allows communication between the two different networks.

---

# Troubleshooting Tickets

## Ticket 01 - Two PCs Cannot Communicate

### Problem

Two PCs connected to the network could not communicate with each other.

### Investigation

I checked:

- IPv4 addresses
- Subnet masks
- Physical connections
- Whether both PCs belonged to the same network

I tested connectivity with:

`ping`

### Cause

One PC was configured in a different IP network.

Example:

PC0:
`192.168.1.10/24`

PC1:
`192.168.2.20/24`

With a `/24` subnet mask, these addresses belong to different networks.

### Solution

I corrected the IP configuration so both computers were in the same network.

Example:

PC0:
`192.168.1.10/24`

PC1:
`192.168.1.20/24`

After the correction, the ping succeeded.

### What I Learned

Devices in the same LAN need compatible IP addressing to communicate directly.

---

## Ticket 02 - Troubleshooting Communication Through a Switch

### Problem

Multiple PCs were connected to a switch and I needed to verify communication between them.

### Investigation

I checked:

- Ethernet connections
- IP configuration
- Link status
- Connectivity using `ping`
- The switch MAC address table

On the Cisco switch I used:

`show mac address-table`

### Result

The switch dynamically learned the MAC addresses of the connected devices after receiving network traffic.

### What I Learned

A switch uses MAC addresses to forward Ethernet frames inside a LAN.

I also learned that a green physical link does not guarantee that the IP configuration is correct.

---

## Ticket 03 - Two Different LANs Cannot Communicate

### Problem

Devices inside each LAN could communicate with each other, but communication between LAN 1 and LAN 2 failed.

### Investigation

I checked:

1. PC IP addresses
2. Subnet masks
3. Router interfaces
4. Default gateways
5. Connectivity using `ping`

### Cause

The two LANs were different IP networks and required a correctly configured router.

### Solution

Router configuration:

LAN 1 interface:
`192.168.10.1/24`

LAN 2 interface:
`192.168.20.1/24`

Default gateways:

LAN 1 PCs:
`192.168.10.1`

LAN 2 PCs:
`192.168.20.1`

After configuring the router interfaces and gateways correctly, communication between both LANs succeeded.

### What I Learned

A router connects different IP networks.

The default gateway of a PC must be a router interface located in the same local network as the PC.

---

## Ticket 04 - Incorrect Router Interface / Default Gateway

### Problem

A PC could communicate inside its own LAN but could not reach another network.

### Investigation

I tested communication step by step with `ping`.

I checked:

- PC IPv4 configuration
- Subnet mask
- Default gateway
- Router interface addressing

### Cause

A router interface was configured with an address belonging to the wrong network.

For example, the router interface connected to:

`192.168.10.0/24`

must have an address from that network, such as:

`192.168.10.1`

and not:

`192.168.20.1`

### Solution

I corrected the router interface IP address and verified the default gateway configuration on the PCs.

### What I Learned

A router interface must belong to the same IP network as the devices connected to that interface.

---

## Ticket 05 - DHCP Failure / APIPA Address

### Problem

A PC configured to obtain its network configuration automatically could not receive a valid IPv4 address.

Instead, Windows/Packet Tracer assigned an address in the range:

`169.254.x.x`

### Investigation

I checked:

- DHCP configuration
- DHCP service
- IP address assigned to the client
- Subnet mask
- Default gateway
- DHCP address pool

### Diagnosis

An address beginning with `169.254.x.x` can indicate that the client was unable to obtain an IPv4 configuration from a DHCP server.

### Solution

I checked the DHCP server configuration and ensured that the DHCP pool contained valid settings for the LAN.

Example:

Network:
`192.168.10.0/24`

Default Gateway:
`192.168.10.1`

DHCP addresses starting from:
`192.168.10.10`

### What I Learned

DHCP automatically provides network configuration to clients.

An APIPA address (`169.254.x.x`) is an important troubleshooting clue when DHCP fails.

---

## Ticket 06 - IP Works but Hostname Does Not

### Problem

The client could communicate with a server using its IP address:

`ping 192.168.10.2`

but communication using a hostname failed.

Example:

`ping www.monserveur.com`

### First Hypothesis

Because communication using the IP address worked, basic network connectivity was available.

The next suspect was DNS resolution.

### Investigation

Useful commands:

`nslookup www.monserveur.com`

`ipconfig /all`

`nslookup` can test whether the hostname can be resolved to an IP address.

`ipconfig /all` can be used to check which DNS server is configured on the Windows client.

### What I Learned

If communication works using an IP address but fails using a hostname, DNS should be investigated.

---

## Ticket 07 - DNS Configuration and Name Resolution

### Configuration

I configured a DNS service in Cisco Packet Tracer.

DNS server:

`192.168.10.2`

I created an A record:

`www.monserveur.com -> 192.168.10.2`

The client was configured to use:

`192.168.10.2`

as its DNS server.

### Test

I tested:

`ping www.monserveur.com`

The hostname was successfully resolved to:

`192.168.10.2`

and the server responded to the ping.

### What I Learned

DNS translates hostnames into IP addresses.

The client must know which DNS server to contact, and the DNS server must contain or be able to obtain the correct record.

---

# Commands Practiced

## Windows

`ipconfig`

Display basic IP configuration.

`ipconfig /all`

Display detailed network configuration, including IP address, subnet mask, default gateway, DHCP and DNS information.

`ping <destination>`

Test IP connectivity.

`nslookup <hostname>`

Test DNS name resolution.

## Cisco IOS

`show mac address-table`

Display the MAC addresses learned by a switch and the associated switch ports.

---

# Troubleshooting Method

During these exercises, I practiced a systematic troubleshooting approach:

1. Confirm the user's problem.
2. Check the physical/network connection.
3. Check the IP address and subnet mask.
4. Test local connectivity.
5. Check the default gateway.
6. Test communication with the destination IP.
7. Check DNS if IP communication works but hostname communication fails.
8. Change one configuration at a time.
9. Test again after the change.

---

# Current Level

These labs cover networking fundamentals for entry-level IT Support and First-Level Support.

My next learning topics include Windows troubleshooting, Linux/SSH, ports, services and additional helpdesk scenarios.
