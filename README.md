# OSPF Multi-Area and BGP Routing Project

## Repository description
A GNS3 routing lab demonstrating multi-area OSPF (Area 0, a Stub Area, and an NSSA), OSPF Area Border Routers (ABRs), Autonomous System Border Routers (ASBRs), and eBGP peering between AS 100, AS 200, and AS 300.

## Project overview
This project is based on the attached GNS3 topology. AS 100 contains the OSPF domain. R1 peers with R8 in AS 200, while R7 peers with R9 in AS 300.

### Routing design

- **OSPF process:** 1
- **Area 0 (backbone):** R1, R2, R3 and the R1–R2 / R1–R3 transit networks
- **Area 1 (Stub):** R2, R4 and R5
- **Area 2 (NSSA):** R3, R6 and R7
- **AS 100:** R1–R7
- **AS 200:** R8
- **AS 300:** R9
- **eBGP peerings:** R1–R8 over `192.168.18.0/24`; R7–R9 over `192.168.79.0/24`
- **LAN:** PC1 is connected to R5 on `192.168.15.0/24`

> Addressing note: the topology image shows abbreviated addresses (for example, `21.1` and `21.2`). This repository expands them to `192.168.21.1` and `192.168.21.2`. The sample configuration assumes `/24` masks on all shown links. Change masks or interface names if your GNS3 nodes use different values.

## Addressing table

| Router | Interface | IP address | Connected network / purpose |
|---|---|---|---|
| R1 | f0/0 | 192.168.18.1/24 | eBGP to R8 |
| R1 | g1/0 | 192.168.21.1/24 | Area 0 to R2 |
| R1 | g2/0 | 192.168.31.1/24 | Area 0 to R3 |
| R2 | g2/0 | 192.168.21.2/24 | Area 0 to R1 |
| R2 | g1/0 | 192.168.42.2/24 | Area 1 to R4 |
| R3 | g1/0 | 192.168.31.3/24 | Area 0 to R1 |
| R3 | g2/0 | 192.168.63.3/24 | Area 2 to R6 |
| R4 | g2/0 | 192.168.42.4/24 | Area 1 to R2 |
| R4 | g1/0 | 192.168.54.4/24 | Area 1 to R5 |
| R5 | g2/0 | 192.168.54.5/24 | Area 1 to R4 |
| R5 | g1/0 | 192.168.15.5/24 | PC1 LAN |
| R6 | g1/0 | 192.168.63.6/24 | Area 2 to R3 |
| R6 | g2/0 | 192.168.76.6/24 | Area 2 to R7 |
| R7 | g1/0 | 192.168.76.7/24 | Area 2 to R6 |
| R7 | g2/0 | 192.168.79.7/24 | eBGP to R9 |
| R8 | f0/0 | 192.168.18.8/24 | eBGP to R1 |
| R9 | g1/0 | 192.168.79.9/24 | eBGP to R7 |

Router IDs from the diagram: R1 `1.1.1.1`, R2 `2.2.2.2`, R3 `3.3.3.3`, R4 `4.4.4.4`, R5 `5.5.5.5`, R6 `6.6.6.6`, R7 `7.7.7.7`, R8 `8.8.8.8`, R9 `9.9.9.9`.

## Files

- `README.md` — project explanation, topology, addressing, and verification.
- `configs/ospf_bgp_configurations.txt` — example Cisco IOS-style configurations and verification commands.

## Suggested setup order

1. Add IP addresses and bring all interfaces up (`no shutdown`).
2. Configure OSPF on R1–R7, including the Stub and NSSA area settings.
3. Configure eBGP between R1–R8 and R7–R9.
4. Confirm OSPF neighbors and BGP neighbors are established.
5. Test routing and end-to-end reachability.

## Verification commands

```text
show ip interface brief
show ip ospf neighbor
show ip ospf
show ip route ospf
show ip route
show ip bgp summary
show ip bgp
show ip protocols
ping <destination-ip>
traceroute <destination-ip>
```

## Important notes

- This is a learning template, not a guaranteed drop-in configuration. GNS3 router images may use different interface names or support different command syntax.
- Configure the `area 1 stub` option consistently on every router participating in Area 1.
- Configure the `area 2 nssa` option consistently on every router participating in Area 2.
- The example uses redistribution between OSPF and BGP at R1 and R7 so that internal OSPF routes and external BGP routes can be exchanged. Redistribution can introduce routing loops or unintended route advertisement in larger networks; use route maps and filtering for production designs.
- The PC1 VPCS address is not visible in the diagram. Example: set PC1 to `192.168.15.10/24` with gateway `192.168.15.5`.
