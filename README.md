# EVPN-VXLAN Solution for Scalable and Flexible Campus Wireless Networks

## Why EVPN-VXLAN ?

Traditional `VLAN-based` solutions for wireless networks come with several limitations that impact scalability, flexibility, and operational efficiency. Below are some of the key challenges and how EVPN-VXLAN addresses them:

### Limitations of Traditional VLAN-Based Solutions

#### Spanning Tree Protocol (STP) Limitations:

- Traditional VLANs rely on Spanning Tree Protocol (STP) to maintain a loop-free topology.
- Alternate paths are blocked by STP, reducing network utilization.
- When a primary path fails, convergence to an alternate path is delayed, impacting network availability.

#### Issues with Port-Channel, vPC, and VSS:

- Traditional link aggregation technologies like Port-Channels, vPC (Virtual Port Channel), and VSS (Virtual Switching System) are tightly coupled to physical topology, limiting flexibility and scalability.
- Failures, such as peer-link or control link outages, can lead to split-brain scenarios, traffic redistribution delays, and service disruptions.
- These technologies require significant manual effort for reconfiguration and troubleshooting, increasing operational complexity and reducing reliability in dynamic environments.

#### Challenges with Wireless Flex Mode:

- Wireless Flex Mode often necessitates extending Layer 2 (L2) domains across the infrastructure.
- This creates a large broadcast domain, which increases the risk of broadcast storms and inefficient use of bandwidth.
- With modern `wireless controllers` frequently hosted in `cloud infrastructure`, extending L2 domains becomes a complex and suboptimal solution.

#### MAC Mobility and Seamless Roaming:

- Device mobility in traditional networks requires MAC address tables to be rebuilt when devices roam across the network.
- The default MAC address aging time `(300 seconds)` may not align with real-time roaming needs, causing disruptions in seamless connectivity.
- This results in degraded user experience due to increased latency and potential packet drops during roaming.

#### Wireless BUM Traffic Challenges:

- Broadcast, Unknown Unicast, and Multicast (BUM) traffic in traditional VLANs propagates throughout the entire Layer 2 domain, consuming bandwidth and processing resources.
- This issue is exacerbated in wireless networks where devices like IoT, VoIP, and multicast-heavy applications generate significant BUM traffic.
- Such traffic leads to increased latency, reduced throughput, and inefficient utilization of network resources.

### How EVPN-VXLAN Solves These Issues

#### Eliminating STP Limitations:

- EVPN-VXLAN eliminates the dependency on Spanning Tree Protocol (STP) by leveraging Equal-Cost Multi-Pathing (ECMP) in the Layer 3 underlay.
- This allows all paths to be active and utilized simultaneously, providing efficient bandwidth utilization and fast convergence without blocking any paths.
- Path failures are quickly resolved using dynamic routing protocols like BGP, ensuring high availability and minimal disruption.

#### Efficient Layer 2 Extension for Wireless Flex Mode:

- EVPN-VXLAN enables scalable Layer 2 extensions over a Layer 3 underlay without creating a single large broadcast domain.
- It uses features like ARP suppression to reduce broadcast traffic and dynamically manages Layer 2 connectivity between endpoints, which is ideal for cloud-based wireless controllers.
- The VXLAN overlay ensures secure and isolated connectivity across a shared infrastructure, simplifying network operations.

#### Optimizing MAC Mobility and Seamless Roaming:

- EVPN enables seamless MAC mobility by leveraging Route Type 2 (MAC/IP Advertisement Routes) to dynamically advertise MAC and IP bindings across the fabric. This ensures real-time updates for device locations as they move within the network.

- MAC Mobility Mechanism:
    - EVPN defines a mechanism to handle smooth MAC address mobility between Network Virtualization Edges (NVEs).
    - The mechanism uses the MAC-mobility extended community in MAC advertisement routes, with a Sequence (SEQ) number to track MAC moves.
    - When a MAC address moves: A BGP update is sent with the `SEQ number incremented by 1 (previous SEQ + 1)` to reflect the new location.
    - If a MAC is learned locally after being EVPN-learned, the SEQ number update ensures the network understands the move.
    - SEQ numbers of zero or absence of the MAC mobility extended community are treated as sequence zero by default.
- This approach ensures:
    - Accurate and consistent MAC address updates across the fabric.
    - Prevention of duplication or misinterpretation during MAC moves.
    - Seamless client roaming across NVEs without disruptions or the need for manual intervention.

- By dynamically adapting to frequent MAC moves, EVPN eliminates the traditional challenges of rebuilding MAC address tables, ensuring a smooth and uninterrupted user experience in wireless networks.

#### Minimizing Wireless BUM Traffic:
- EVPN-VXLAN significantly reduces `Broadcast, Unknown Unicast, and Multicast (BUM)` traffic by leveraging:

    - `ARP Suppression`: Prevents broadcast ARP requests by responding locally when possible.
    - `Multicast Replication`: Efficiently replicates multicast traffic only to relevant endpoints using IGMP snooping and EVPN control plane mechanisms.
    - This ensures efficient bandwidth utilization and enhances performance for wireless networks with heavy multicast or IoT traffic.

#### Addressing Issues with Port-Channel, vPC, and VSS:

- EVPN-VXLAN eliminates the dependency on traditional link aggregation technologies like Port-Channels, vPC, and VSS by decoupling the logical overlay from the physical underlay.
- ECMP in the underlay provides path redundancy without requiring peer-link or control link configurations.
- The overlay operates independently of physical topologies, ensuring flexibility, scalability, and simplified operations.
- Split-brain scenarios are avoided as the control plane dynamically learns and advertises reachability information, ensuring consistency and resiliency.

## Key EVPN Route Types in VXLAN

### Route Type 2 (MAC/IP Advertisement Route):
- Purpose: Advertises MAC and IP bindings for endpoints across the fabric.
- Role: Ensures dynamic and seamless mobility for devices, particularly important in wireless networks where clients frequently roam.

- `Key Benefits:`
    - Allows the network to know which VTEP (VXLAN Tunnel Endpoint) is hosting a specific MAC/IP address.
    - Supports seamless Layer 2 connectivity and fast endpoint discovery.

### Route Type 3 (Inclusive Multicast Ethernet Tag Route):
- Purpose: Facilitates BUM traffic handling (Broadcast, Unknown Unicast, and Multicast).
- Role: Advertises multicast group information for VXLAN tunnels, enabling efficient replication of BUM traffic only to the VTEPs participating in a given VLAN or VXLAN segment.
- `Key Benefits`:
    - Reduces unnecessary traffic by ensuring BUM traffic is forwarded only to relevant VTEPs.
    - Enhances bandwidth efficiency and reduces latency in large-scale networks.

### Route Type 5 (IP Prefix Advertisement Route):

- Purpose: Advertises IP prefixes for Layer 3 routing in the overlay network.
- Role: Enables inter-subnet routing across the VXLAN fabric.
- `Key Benefits`:
    - Simplifies Layer 3 connectivity by using the same EVPN control plane for both Layer 2 and Layer 3 routes.
    - Ensures scalable and efficient routing across distributed network segments.

## EVPN-VXLAN Lab:

![Diagram](https://github.com/Diptiranjan9/EVPN-VXLAN_Campus-Wireless-Modernization/blob/main/snapshots/Evpn-Vxlan-Lab-Diagram.png)

In this lab, I have configured a small network setup using two core switches in a stack and two leaf switches. The configuration files for this setup are included in the repository. In this design, the spine switches also act as the border switches, making it suitable for smaller networks. For larger setups, you can designate one of the border leaf switches as a dedicated border switch and avoid stacking both spine switches. 

## Key Takeaways

- Overcoming Traditional VLAN Challenges: EVPN-VXLAN addresses issues like STP limitations, inefficient Layer 2 extensions, and MAC mobility challenges, making it more scalable and flexible for campus wireless networks.
- Eliminating STP Dependencies: By using ECMP and BGP for faster convergence and path optimization, it improves bandwidth utilization and network availability.
- Improved Wireless Flex Mode: EVPN-VXLAN offers efficient Layer 2 extensions over Layer 3 without large broadcast domains, ideal for cloud-based wireless controllers.
- Optimized MAC Mobility: The lab demonstrates seamless MAC mobility, ensuring smooth roaming for devices.








