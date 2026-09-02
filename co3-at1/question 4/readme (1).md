## Traffic Handling by Network Devices

### 1. Unicast Traffic (One-to-One)

* **Switch (Layer 2 Handling):**
  * Reads the incoming frame's **Destination MAC Address**.
  * Looks up the address in its **MAC Address Table**:
    * **Known MAC:** Forwards the frame strictly out of the single corresponding egress port.
    * **Unknown MAC:** Performs *Unknown Unicast Flooding* out of all ports within the same VLAN (except the ingress port) until an ARP response learns the port location.
  * Preserves frame encapsulation without modifying MAC headers.

* **Router (Layer 3 Handling):**
  * De-encapsulates the Layer 2 Ethernet frame to inspect the **Destination IP Address** at Layer 3.
  * Checks its **Routing Table (FIB)** for a matching route or Default Gateway.
  * Decrements the **TTL (Time to Live)** value by 1.
  * Re-encapsulates the packet with a new Layer 2 frame (Source MAC = Router egress interface, Destination MAC = Next-hop IP/Destination host MAC via ARP).
  * Forwards the frame out of the appropriate egress interface.

---

### 2. Broadcast Traffic (One-to-All within Subnet)

* **Switch (Layer 2 Handling):**
  * Detects the broadcast MAC address.
  * Replicates and **floods the frame out of every active port** within the local broadcast domain (VLAN), excluding the port where the packet originated.
  * Does not drop or block the broadcast; ensures every attached host on the switch receives a copy.

* **Router (Layer 3 Handling):**
  * Inspects the destination IP (`255.255.255.255` or subnet-directed broadcast like `192.168.1.255`).
  * Processes the broadcast frame locally on the ingress interface.
  * **Drops / Terminates the broadcast by default**: Routers define the boundary of a broadcast domain and will never forward broadcast packets across to other interfaces or subnets, preventing broadcast storms.

---

### 3. Multicast Traffic (One-to-Subscribed Group)

* **Switch (Layer 2 Handling):**
  * **Default Mode (Without IGMP Snooping):** Treats multicast frames like broadcast frames and floods them across all ports in the VLAN.
  * **Optimized Mode (With IGMP Snooping enabled):** Inspects IGMP membership join/leave messages, maps multicast MAC addresses, and forwards multicast frames **only to ports that have active subscribers**.

* **Router (Layer 3 Handling):**
  * Inspects the Class D destination IP address (`224.0.0.0` to `239.255.255.255`).
  * **Default Behavior:** Drops multicast traffic unless explicitly configured with multicast routing.
  * **When Multicast Routing is Active (PIM / Routing Protocols):** Uses multicast group tables to replicate and route packets only down active branches of the multicast distribution tree where interested receivers reside.
