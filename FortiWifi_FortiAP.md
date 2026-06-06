## Secure Wireless LAN 7.4

### Topic: Managed AP Topologies

# 📄 PAGE 93 — Managed AP Topologies (Overview)

## 🖥️ SLIDE SUMMARY

The slide introduces **four physical topologies** for connecting FortiAP to the FortiGate wireless controller:

| # | Topology                | Alt Name                     |
|---|-------------------------|------------------------------|
| 1 | **Direct Connection**   | Wire Closet Deployment       |
| 2 | **Switched Connection** | Gateway Deployment           |
| 3 | **Connection over WAN** | Datacenter Remote Management |
| 4 | **Wireless Mesh**       | Distributed/Mesh Deployment  |

The slide also notes that these topologies use **two traffic modes**:

- **Tunnel Mode**
- **Bridge Mode**

## 📋 DETAILED INSTRUCTOR NOTES

### Why Do Topologies Matter?

Before you deploy even a single FortiAP, you must understand **how it will physically reach the FortiGate wireless controller**. The topology you choose affects:

- Which **discovery method** the AP uses
- Whether you need to **pre-configure the AP**
- What **firewall ports** must be open
- How **user traffic flows** through the network

Think of it like building roads before putting cars on them — the road design (topology) determines how fast, how far, and how reliably the cars (data packets) can travel.

---

### 🔌 Topology 1 — Direct Connection (Wire Closet)

- FortiAP is **plugged directly into a FortiGate port**
- No switches or routers between them
- FortiAP discovers FortiGate using **broadcast** — the simplest method
- Ideal when the number of APs **equals** the number of FortiGate internal ports

---

### 🔀 Topology 2 — Switched Connection (Gateway)

- FortiAP connects through **L2 switches or L3 routers**
- **CAPWAP ports 5246 and 5247 must be open** throughout the path
- Discovery uses **Static IP, DHCP Option 138, or DNS**
- Most common in **enterprise and campus** environments

---

### 🌐 Topology 3 — Connection over WAN (Datacenter Remote)

- The FortiGate wireless controller is **offsite** — in a datacenter or cloud
- Best practice: pre-configure FortiAP with a **static controller IP**
- You can set **up to 3 controller IPs** for failover redundancy
- All CAPWAP traffic crosses the public internet — ports 5246/5247 must be open on all routers and firewalls along the path

---

### 📡 Topology 4 — Wireless Mesh (Distributed)

- APs **relay traffic wirelessly** through neighboring APs back to the root
- The **root AP** is the only one physically cabled to the network
- Eliminates the need to run cables everywhere — ideal for outdoor or hard-to-wire environments
- The root AP can be a **FortiAP or FortiWiFi** device

---

### 🚦 Two Traffic Modes (Preview)

Every topology above operates in one of two modes:

- **Tunnel Mode:** All wireless client traffic is encapsulated inside CAPWAP and sent back to FortiGate for security inspection before going anywhere
- **Bridge Mode:** Wireless client traffic exits **locally** at the AP — FortiGate only manages the AP, not every packet

> 💡 **Trainer Tip:** Tunnel mode = more security, more load on FortiGate. Bridge mode = more performance, less centralized control. You'll choose based on your security policy and network size.

---

---

# 📄 PAGE 94 — Direct Connection

---

## 🖥️ SLIDE SUMMARY

The slide illustrates the **simplest FortiAP deployment**:

```
Internet ←→ FortiGate (Wireless Controller)
                    ↕ [Direct Cable]
                  FortiAP
```

**Key bullet points from the slide:**

- FortiAP connected **directly** to FortiGate — no L3 devices in between
- **Easiest** deployment method
- **No need to pre-configure** the AP
- Controller discovered automatically using **broadcast**
- Ideal for **home or small office**

Three traffic types shown:

- 🔵 **Management** (HTTP, SSH)
- 🟡 **Control** (CAPWAP)
- 🟢 **Data** (user traffic)

---

## 📋 DETAILED INSTRUCTOR NOTES

### The "Wire Closet" Concept

The name *wire closet deployment* comes from traditional IT environments where network equipment lives in a small closet. FortiGate sits in that closet, and FortiAP cables run directly from it — no switch in between.

### How Discovery Works Here

When FortiAP boots up and is directly connected to FortiGate:

1. FortiAP sends a **DHCP request** → FortiGate assigns it an IP address from its built-in DHCP server
2. FortiAP enters **discovery mode** and sends a **broadcast** discovery request
3. Because FortiGate is on the same broadcast domain (directly connected), it **immediately receives and responds** to the broadcast
4. CAPWAP tunnel is established — FortiAP is online

> 💡 **Real-world analogy:** Imagine shouting "Is anyone there?" in a small room — the person standing right next to you hears it instantly. That's broadcast discovery in a direct connection.

### When to Use Direct Connection

- Number of FortiAPs **≤ number of FortiGate internal ports**
- Small branch office with **1–4 APs**
- Home office or **teleworker** setup
- Situations where **simplicity > scalability**

### What You Don't Need to Do

- ❌ No static IP configuration on the AP
- ❌ No DHCP option configuration
- ❌ No DNS setup
- ❌ No firewall rules for CAPWAP (FortiGate is the firewall)

> ⚠️ **Trainer Warning:** This topology does NOT scale. If you have 20 APs, you'd need 20 FortiGate ports — which is unrealistic. For anything beyond a handful of APs, move to a Switched Connection.

---

---

# 📄 PAGE 95 — Switched Connection

---

## 🖥️ SLIDE SUMMARY

The slide shows FortiAP connecting to FortiGate **through a switch**:

```
Internet ←→ FortiGate (Wireless Controller)
                    ↕ [FortiLink or uplink]
                  Switch (L2 / L3)
                 ↙        ↘
            FortiAP      FortiAP
```

**Key bullet points from the slide:**

- FortiAP connected through an **L2 or L3 device**
- L3 device must **forward traffic** between FortiGate and FortiAPs
- **Routable path** must exist between FortiAP and FortiGate
- **Ports 5246 and 5247** must be open (CAPWAP)
- Discovery via **Static IP, DHCP Option 138, or DNS**
- Great for **edge gateway, enterprise campus, or HQ** deployments

---

## 📋 DETAILED INSTRUCTOR NOTES

### L2 vs L3 Switch — What Changes?

This is a critical distinction:

| Scenario                                   | Same Subnet? | Discovery Method                | Notes                                 |
|--------------------------------------------|--------------|---------------------------------|---------------------------------------|
| FortiAP → **L2 Switch** → FortiGate        | ✅ Yes        | Broadcast or Multicast          | Behaves almost like direct connection |
| FortiAP → **L3 Switch/Router** → FortiGate | ❌ No         | Static, DNS, or DHCP Option 138 | Routable path required                |

> 💡 **Key Concept:** Broadcasts do NOT cross L3 boundaries. If your AP is on a different subnet than FortiGate, broadcast discovery will **fail**. You must use Static IP, DNS, or DHCP Option 138.

---

### DHCP Option 138 — Deep Dive

When FortiAP sends a DHCP request, the DHCP server can include **Option 138** in the reply — this tells the AP the IP address of its wireless controller.

**How to encode the IP:**

- Convert each octet of the FortiGate IP to **hexadecimal** separately
- Concatenate them

**Example:**

```
FortiGate IP: 192.168.0.1
192 = C0
168 = A8
0   = 00
1   = 01
→ Option 138 value: C0A80001
```

> 💡 **Real-world analogy:** DHCP Option 138 is like a hotel concierge handing you a note that says "Your meeting is in Room 138" — the FortiAP doesn't have to search; it's told exactly where to go.

---

### FortiSwitch + FortiLink Integration

The slide specifically mentions **FortiSwitch** connected via **FortiLink**:

- **FortiLink** is a Fortinet proprietary protocol that integrates FortiSwitch management **directly into FortiGate's GUI**
- You manage the switch from FortiGate — no separate switch management interface needed
- This is part of Fortinet's **Security Fabric** — single-pane-of-glass management

**Traffic flow in this setup:**

```
AP management traffic → directly to FortiGate (control plane)
User/device traffic → AP → Switch → FortiGate → Internet (data plane)
```

---

### Why Ports 5246 and 5247 Must Be Open

- **UDP 5246** = CAPWAP **control channel** (configuration, management commands)
- **UDP 5247** = CAPWAP **data channel** (user traffic in tunnel mode)

If any switch ACL, firewall rule, or router blocks these ports → the AP **cannot communicate** with FortiGate, even if it discovers it.

> ⚠️ **Trainer Warning:** This is one of the most common deployment mistakes. Always verify these ports are open end-to-end before going on-site.

---

---

# 📄 PAGE 96 — Connection over WAN

---

## 🖥️ SLIDE SUMMARY

The slide shows a **remote FortiAP** connecting to an **offsite FortiGate** over the internet:

```
Remote FortiGate          Internet          FortiAP + Router
(Wireless Controller) ←←←←←←←←←←←←←←←←←← (Remote Site)
```

**Key bullet points from the slide:**

- FortiGate Wi-Fi controller is **off-premises**
- **Routable path** required between FortiAP and FortiGate
- **Ports 5246 and 5247** must be open
- Discovery via **Static IP, DHCP Option, or DNS**
- Can work as a **pre-configured AP for remote workers**

---

## �� DETAILED INSTRUCTOR NOTES

### The Remote Worker Scenario — Step by Step

This topology is designed for employees working from home or in micro-branches with no local IT.

Here's how it works in practice:

1. IT pre-configures FortiAP with the **static public IP** of the corporate FortiGate
2. FortiAP is shipped to the employee's home
3. Employee plugs it into their home router
4. FortiAP automatically contacts the corporate FortiGate **over the internet**
5. A **CAPWAP tunnel** is established — encrypted with **DTLS**
6. FortiAP broadcasts the **same corporate SSID** as in the office
7. All traffic from the employee's devices is **sent back to FortiGate** for inspection

> 💡 **Real-world analogy:** It's like giving an employee a VPN phone — wherever they plug it in, it automatically connects back to HQ and behaves as if they're in the office.

---

### Three Controller IPs for Redundancy

You can configure **up to 3 FortiGate controller IP addresses** on each FortiAP:

- If the primary FortiGate goes down → FortiAP automatically fails over to the **second IP**
- If that fails → it tries the **third IP**

This provides **high availability** for remote deployments where local IT cannot intervene.

---

### Default Traffic Behavior

By default in WAN topology:

- **All traffic** from wireless clients is sent back to FortiGate through the CAPWAP tunnel
- FortiGate **inspects everything** — web filtering, IPS, application control all apply
- This is **Tunnel Mode** behavior

> ⚠️ **Trainer Warning:** Sending all traffic back through the CAPWAP tunnel over WAN increases **latency** and consumes **WAN bandwidth**. For high-traffic remote sites, consider **Split Tunneling** — local traffic exits locally, only corporate traffic goes back to FortiGate.

---

### Security Consideration

The CAPWAP tunnel uses **DTLS (Datagram TLS)** encryption:

- DTLS is TLS adapted for **UDP** (since CAPWAP uses UDP, not TCP)
- Protects the management and data channels from eavesdropping over the public internet
- This is especially critical in WAN deployments where traffic crosses untrusted networks

---

---

# 📄 PAGE 97 — Wireless Mesh

---

## 🖥️ SLIDE SUMMARY

The slide shows a wireless mesh topology diagram:

```
         Leaf FortiAP
         ↕ 2.4 GHz (Client SSID)
         ↕ 5 GHz (Mesh/Backhaul SSID)
    Mesh Root FortiAP ←[cable]→ FortiGate
         ↕ 5 GHz (Mesh/Backhaul SSID)
         ↕ 2.4 GHz (Client SSID)
         Leaf FortiAP
```

**Key bullet points from the slide:**

- Eliminates the need to **physically cable every AP**
- Best practice on two-radio AP:
  - One radio = **Backhaul SSID** (carries CAPWAP control + data)
  - One radio = **Client SSID** (serves user devices)
- Use **5 GHz for backhaul** — best practice
- Better performance due to **fewer collisions** between backhaul and client traffic

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Wireless Mesh Exists

Running ethernet cable everywhere is expensive, disruptive, and sometimes physically impossible — think:

- Historic buildings with no cable ducts
- Large outdoor areas (parking lots, campuses, stadiums)
- Warehouses with concrete pillars
- Temporary event venues

Wireless mesh solves this by letting APs **talk to each other wirelessly** and relay traffic back to the one cabled AP (the root).

---

### The Four Mesh Roles — Explained

| Role            | Connected To                         | Function                         |
|-----------------|--------------------------------------|----------------------------------|
| **Mesh Root**   | Physical cable to FortiGate          | Gateway for all mesh traffic     |
| **Branch AP**   | Wirelessly to Root or another Branch | Relay node between Root and Leaf |
| **Leaf AP**     | Wirelessly to Root or Branch         | Serves end-user clients          |
| **End Clients** | Wirelessly to Leaf                   | Laptops, phones, tablets         |

> 💡 **Real-world analogy:** Think of the mesh like a bucket brigade passing water. The root is the water source. Each AP passes the "bucket" (data) down the line to reach the user.

---

### Why 5 GHz for Backhaul? — Technical Deep Dive

**5 GHz advantages for backhaul:**

- More **available channels** — less interference from neighboring networks
- Higher **throughput capacity** — can handle the aggregated traffic of all leaf APs
- Less congested than 2.4 GHz (fewer consumer devices use 5 GHz for mesh)

**The two-radio best practice:**

```
Radio 1 (5 GHz) → Backhaul SSID (AP-to-AP traffic only)
Radio 2 (2.4 GHz) → Client SSID (user device traffic)
```

This separation ensures backhaul and client traffic **never compete** for the same radio's airtime.

> ⚠️ **What happens if you mix them on one radio?**
>
> - Backhaul and client devices share airtime
> - Every time an AP relays traffic to the root, it **steals bandwidth** from connected clients
> - Performance degrades significantly — especially at the leaf level

---

### Background Scanning Trade-off

In a normal (non-mesh) FortiAP deployment with 3 radios, one radio can be dedicated to **24/7 rogue AP scanning**.

In a two-radio mesh AP:

- Radio 1 = Backhaul SSID (dedicated)
- Radio 2 = Client SSID
- **No radio left for background scanning** ❌

**Alternative:** Share the backhaul SSID and client SSID on one radio — but this **reduces performance** significantly.

> 💡 **Trainer Tip:** This is a real trade-off you face in mesh deployments. If rogue AP detection is a security requirement, you need **three-radio FortiAP models** for mesh deployments.

---

### Site Survey — Why It's Critical

Wireless mesh relies entirely on **signal quality between APs**. Problems like:

- Physical obstructions (walls, metal shelving, machinery)
- RF interference from other equipment
- Distance between APs

...can all cause the mesh backhaul to **drop or underperform**. A **site survey** identifies these issues before deployment — not during a failed production rollout.

> ⚠️ **Trainer Warning:** "A wireless medium is not considered reliable for backhaul connectivity." This is directly from the Fortinet guide. Never use wireless mesh for mission-critical backhaul without proper planning and a site survey.

---

---

# 📄 PAGE 98 — Mesh With Wireless Bridge

---

## 🖥️ SLIDE SUMMARY

The slide shows a **point-to-point wireless bridge** connecting two separate wired network segments:

```
[Wired LAN Segment A] ←→ Root FortiAP
                              ↕ 5 GHz (40 MHz) → 200 Mbps max
                          Branch FortiAP ←→ [Wired LAN Segment B]
                              ↕ 2.4 GHz (20 MHz) → 150 Mbps max
```

**Key technical details from the slide:**

- 5 GHz at **40 MHz wide** = **200 Mbps max rate**
- 2.4 GHz at **20 MHz wide** = **150 Mbps max rate**
- **Only one radio** can be used for the mesh SSID
- External **N-type directional antennas** supported
- Two LAN segments connected over a **wireless backhaul SSID**

---

## �� DETAILED INSTRUCTOR NOTES

### Wireless Bridge vs. Wireless Mesh — What's the Difference?

| Feature          | Wireless Mesh                 | Wireless Bridge                             |
|------------------|-------------------------------|---------------------------------------------|
| **Purpose**      | Extend wireless client access | Connect two **wired** network segments      |
| **Clients**      | Leaf APs serve user devices   | No direct client connections on bridge link |
| **Root AP type** | FortiAP **or** FortiWiFi      | **FortiAP only** (not FortiWiFi)            |
| **Use case**     | Large outdoor/indoor coverage | Building-to-building links                  |

> 💡 **Real-world analogy:** A wireless bridge is like a radio link between two buildings — instead of digging trenches to lay fiber, you shoot a wireless signal between rooftops.

---

### Channel Width and Speed — Technical Explanation

The slide gives specific numbers — let's understand why:

**5 GHz radio — 40 MHz channel width:**

- Wider channel = more data carried simultaneously
- 40 MHz channels are standard for 802.11n/ac backhaul links
- Max rate: **\~200 Mbps** (practical throughput, not theoretical max)

**2.4 GHz radio — 20 MHz channel width:**

- 2.4 GHz has fewer non-overlapping channels (only 3: 1, 6, 11)
- 40 MHz on 2.4 GHz would cause massive interference — so 20 MHz is used
- Max rate: **\~150 Mbps**

> �� **Trainer Tip:** Always use 5 GHz for the bridge link if possible — more channels, less interference, higher throughput. Use 2.4 GHz only if distance requires it (2.4 GHz penetrates obstacles better).

---

### Configuration Steps — How to Set It Up

**Step 1:** Configure a **backhaul SSID** on the Root FortiAP (same as standard mesh)

**Step 2:** Configure the **Root FortiAP** as the mesh root

- ⚠️ **Must be FortiAP** — FortiWiFi cannot be the root in a bridge setup

**Step 3:** On the **Branch FortiAP**, enable **bridging mode**

- This tells the branch AP to bridge its wired LAN port with the wireless backhaul link

**Step 4:** The two wired LAN segments are now connected wirelessly

---

### Directional Antennas — Why They Matter

The slide mentions **external N-type directional antennas** are supported. Here's why this matters:

**Omnidirectional antennas** (standard on most APs):

- Radiate signal in **all directions** — like a light bulb
- Good for covering an area with clients in all directions
- Wastes signal in unwanted directions for point-to-point links

**Directional antennas** (for bridges):

- Focus signal like a **flashlight** — in one direction only
- Much stronger signal over longer distances
- Less interference from/to other devices
- N-type connector = weatherproof, suitable for **outdoor** installations

> 💡 **Real-world analogy:** Using a directional antenna for a building-to-building bridge is like using a laser pointer instead of a lamp. All the energy goes exactly where you need it.

---

### The "One Radio" Limitation

The slide notes: *"Only one of the radios can be used for the mesh SSID."*

This means:

- On a **two-radio AP**: one radio handles the bridge link, the other is available for clients or scanning
- You **cannot** have both radios participating in different bridge links simultaneously
- Plan accordingly — if you need redundant bridge paths, you need additional hardware

---

---

# 📊 Master Summary — Pages 93–98

```
┌─────────────────────────────────────────────────────────────────┐
│              MANAGED AP TOPOLOGY DECISION GUIDE                 │
├────────────────┬──────────────┬──────────────┬─────────────────┤
│ Topology       │ Best For     │ Discovery    │ Key Requirement │
├────────────────┼──────────────┼──────────────┼─────────────────┤
│ Direct         │ Small/Home   │ Broadcast    │ Direct cable    │
│ Switched       │ Enterprise   │ DHCP/DNS/    │ Ports 5246/5247 │
│                │ Campus       │ Static       │ open            │
│ WAN            │ Teleworker   │ Static IP    │ Pre-configured  │
│                │ Remote Site  │ recommended  │ controller IP   │
│ Mesh           │ No-cable     │ Wireless     │ Site survey     │
│                │ areas        │ backhaul     │ required        │
│ Bridge         │ Building-to- │ Wireless     │ FortiAP root    │
│                │ building     │ backhaul     │ only            │
└────────────────┴──────────────┴──────────────┴─────────────────┘
```

### Topic: FortiLAN Cloud — Cloud-Based AP Management

---

---

# 📄 PAGE 102 — FortiLAN Cloud Licensing

---

## 🖥️ SLIDE SUMMARY

The slide outlines the **free basic license** included with FortiLAN Cloud and mentions additional paid options:

**Free Basic License includes:**

| Feature             | Limit                            |
|---------------------|----------------------------------|
| FortiAP devices     | Up to **30 APs** per account     |
| FortiSwitch devices | Up to **3 switches** per account |
| Sites               | Up to **3 sites**                |
| Log retention       | **7 days**                       |

**Additional paid licenses available for:**

- FortiAP **advanced management**
- FortiSwitch **advanced management**
- **FortiCloud Premium**
- **Multi-tenancy**

---

## �� DETAILED INSTRUCTOR NOTES

### What Is FortiLAN Cloud?

**FortiLAN Cloud** is Fortinet's **cloud-based management platform** for FortiAP and FortiSwitch devices. Unlike FortiGate-managed APs (which require an on-premises controller), FortiLAN Cloud lets you manage APs **entirely from the internet — no local FortiGate required**.

> 💡 **Real-world analogy:** Think of FortiLAN Cloud like managing your home security cameras from a smartphone app — you don't need a local server in your house; everything is handled in the cloud.

---

### Free vs. Paid — When to Upgrade?

The **free basic license** is generous for small deployments:

- A single business with up to 3 sites and 30 APs = completely free
- Logs are retained for **7 days** — enough for basic troubleshooting

**When you need to upgrade:**

- More than 30 APs → paid FortiAP advanced management license
- More than 3 switches → paid FortiSwitch advanced management license
- Managing multiple customers (MSP model) → **Multi-tenancy license**
- Longer log retention, advanced analytics → **FortiCloud Premium**

> ⚠️ **Trainer Warning:** The 7-day log retention limit on the free tier is a real constraint in production. If you ever need to investigate a security incident that happened 10 days ago — you'll have no logs. Always plan your license tier based on **compliance and retention requirements**.

---

### How to Access FortiLAN Cloud

Two URLs to remember:

- Via Fortinet support portal: [`https://support.fortinet.com`](https://support.fortinet.com) → Services → FortiLAN Cloud
- Direct portal: [`https://fortilan.forticloud.com`](https://fortilan.forticloud.com)

To compare license features:

- Inside the portal → click the **FortiLAN Cloud feature reference icon** → click **Feature Availability**

---

---

# 📄 PAGE 103 — Key Benefits of FortiAP Managed by FortiLAN Cloud

---

## 🖥️ SLIDE SUMMARY

The slide presents **5 key benefits** of using FortiLAN Cloud to manage FortiAP devices:

| Benefit                        | Description                                                                                        |
|--------------------------------|----------------------------------------------------------------------------------------------------|
| ⚡ **Zero-Touch Deployment**    | Bulk import via FortiCloud key; simple provisioning via **FortiZTP**                               |
| 🖥️ **Single-Pane Management** | Multiple dashboards for APs, radios, clients; event logs, alarms, spectrum analysis, iPerf, alerts |
| 📈 **Scalability**             | From a few devices to **thousands** — same platform grows with you                                 |
| 🏢 **Multi-Tenancy**           | One license manages many customers; role-based access control (RBAC)                               |
| 🔒 **Security at Edge**        | **FortiAP-U** models enable security services (UTP) at the network edge                            |

---

## 📋 DETAILED INSTRUCTOR NOTES

### Benefit 1 — Zero-Touch Deployment (ZTP)

Traditional AP deployment requires a technician on-site to:

- Plug in a laptop
- Access the AP's local GUI
- Manually configure controller IP, SSID, security settings

**FortiZTP eliminates all of this:**

- APs are registered to FortiCloud **before being shipped**
- When plugged in at the remote site, they automatically phone home to FortiLAN Cloud
- No on-site technical expertise needed
- Configuration is pushed down from the cloud instantly

> 💡 **Real-world analogy:** It's like ordering a pre-configured smartphone — you take it out of the box, connect to Wi-Fi, and it's already set up with your apps and settings. No IT person needed.

---

### Benefit 2 — Single-Pane Management (Ease of Troubleshooting)

FortiLAN Cloud provides a **unified dashboard** showing:

- All **managed APs** and their status
- All **radios** and their channel/power settings
- All **connected clients** and their usage
- **Neighboring networks** (useful for RF planning and rogue detection)
- **Event logs and alarms** — filterable and searchable
- **Spectrum analysis** — visual RF environment view
- **iPerf** — built-in throughput testing tool to diagnose bandwidth issues
- **Alerts** — proactive notifications for issues

> 💡 **iPerf explained:** iPerf is a network performance measurement tool. From FortiLAN Cloud, you can run iPerf tests directly from the AP to measure real-world throughput — no separate tools or physical access needed.

---

### Benefit 3 — Scalability

FortiLAN Cloud is a **true cloud architecture**, meaning:

- Adding more APs doesn't require upgrading hardware controllers
- The platform scales elastically — from 5 APs for a small café to **tens of thousands** for a national retail chain
- Firmware updates, configuration changes, and monitoring work the same way at any scale

---

### Benefit 4 — Multi-Tenancy (MSP Use Case)

**Multi-tenancy** means one FortiLAN Cloud account can manage **multiple separate customers** (tenants), each with:

- Their own **isolated view** of their network
- Their own **read-only customer account** (with their company logo on reports)
- **Role-based access control (RBAC)** — different admins see different things

> 💡 **Real-world analogy:** Like a property management company that manages 50 apartment buildings — each tenant has their own key and sees only their apartment, but the manager can see everything.

This makes FortiLAN Cloud perfect for **Managed Service Providers (MSPs)** who manage wireless networks for multiple businesses.

---

### Benefit 5 — Security at the Edge (FortiAP-U)

Standard FortiLAN Cloud-managed APs are just wireless access points — no security services built in.

**FortiAP-U models** add **UTP (Unified Threat Protection)** directly at the AP level:

- 🦠 Antivirus
- 🤖 Anti-botnet
- 🌐 Web filtering
- 🛡️ IPS
- 📱 Application control

This is critical for **remote branches** or **standalone deployments** where there is no local FortiGate to inspect traffic.

> ⚠️ **Trainer Warning:** UTP on FortiAP-U requires an **additional license** — it is NOT included in the basic FortiLAN Cloud license. Factor this into your deployment cost planning.

---

---

# 📄 PAGE 104 — FortiLAN Cloud Features

---

## 🖥️ SLIDE SUMMARY

The slide lists features available in both the **free** and **premium** FortiLAN Cloud tiers:

**Free Basic Features:**

- Manage and deploy wireless networks
- Built-in **guest management** with self-registration
- Deploy **protection profiles**
- **Scheduled upgrades**
- Local or remote **RADIUS server** support
- **AP location maps**
- **Real-time network visibility**

**Premium Account adds:**

- **Multi-tenancy** — multiple customers from single GUI
- **Independent subaccounts**
- Assign AP networks to **subaccounts**

---

## �� DETAILED INSTRUCTOR NOTES

### Feature Deep Dives

#### 🧑‍💻 Guest Management with Self-Registration

FortiLAN Cloud includes a built-in **guest portal** — no extra software needed:

- Guests connect to the guest SSID and are redirected to a **captive portal**
- They can **self-register** with their name/email — no admin intervention needed
- Admins can set **time limits, bandwidth limits, and access restrictions** per guest

> 💡 **Real-world analogy:** Like a hotel Wi-Fi login page — guests sign in themselves, the system tracks who accessed what, and access expires after checkout.

---

#### 🔒 Protection Profiles

**Protection profiles** define security rules applied to APs:

- Which SSIDs are allowed
- What encryption standards are enforced
- Intrusion detection settings
- Client isolation rules

These profiles are **pushed from the cloud** — change the profile once, and it updates across all assigned APs simultaneously.

---

#### 📅 Scheduled Upgrades

Firmware upgrades in FortiLAN Cloud can be **scheduled** to happen:

- At a specific date and time (e.g., 2 AM on Sunday)
- On selected APs or groups of APs
- With automatic rollback if the upgrade fails

> ⚠️ **Trainer Tip:** Always schedule firmware upgrades during **maintenance windows** — an AP reboots during a firmware upgrade, causing a brief wireless outage. Scheduling at 2 AM prevents user complaints.

---

#### 📡 RADIUS Authentication Support

FortiLAN Cloud supports both:

- **Local RADIUS** — authentication server within the local network
- **Remote RADIUS** — cloud-based or datacenter RADIUS server

This allows **802.1X enterprise authentication** (username/password per user) rather than just a shared Wi-Fi password — critical for corporate security policies.

---

#### 🗺️ AP Location Maps

You can **upload a floor plan** image into FortiLAN Cloud and **place AP icons** on the map at their physical locations. This gives:

- Visual overview of AP coverage
- Quick identification of offline APs by location
- Easier troubleshooting ("AP in the east wing conference room is offline")

---

#### 🏢 Premium — Multi-Tenancy Details

For **MSPs** managing multiple clients:

- Each client gets an **independent subaccount**
- The MSP admin has a **master view** of all clients
- Each client can log in and see **only their own network**
- Client-facing reports include the **customer's own logo** — professional white-label presentation

---

---

# 📄 PAGE 105 — FortiLAN Cloud AP Management

---

## 🖥️ SLIDE SUMMARY

The slide illustrates the **traffic flow** between a standalone FortiAP and FortiLAN Cloud:

```
FortiLAN Cloud (Internet)
        ↕  HTTPS TCP 443     → AP Discovery
        ↕  CAPWAP UDP 5246   → Configuration, Event Logs, Statistics
        ↕  CAPWAP UDP 5247   → Data
      [Router]
        ↕
     FortiAP (Standalone)
```

**Key points from the slide:**

- Supports **standalone FortiAP** deployment — no local FortiGate needed
- Supports **FortiAP-U** series with UTP security at the edge
- FortiAP initiates connection to FortiLAN Cloud on **HTTPS TCP 443** for discovery
- Configuration, logs, and stats use **CAPWAP UDP 5246 and 5247**
- Wireless client traffic is **bridged locally** — NOT tunneled to FortiLAN Cloud

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Most Critical Concept on This Page: Traffic Behavior

This is where FortiLAN Cloud differs **fundamentally** from FortiGate-managed APs:

| Feature             | FortiGate-Managed                                          | FortiLAN Cloud-Managed                                    |
|---------------------|------------------------------------------------------------|-----------------------------------------------------------|
| Control plane       | CAPWAP to FortiGate                                        | CAPWAP to FortiLAN Cloud                                  |
| User traffic        | Tunneled to FortiGate (Tunnel Mode) OR local (Bridge Mode) | **Always bridged locally** — never sent to FortiLAN Cloud |
| Security inspection | FortiGate inspects all traffic                             | Only FortiAP-U can inspect locally (UTP)                  |

> 💡 **Key Insight:** FortiLAN Cloud is a **management plane only** — it controls the AP's configuration but does NOT handle user traffic. All client data stays local. This is important for **bandwidth planning** — you don't consume WAN bandwidth sending user traffic to the cloud.

---

### Port and Protocol Summary

| Purpose                     | Protocol | Port         |
|-----------------------------|----------|--------------|
| **AP Discovery**            | HTTPS    | **TCP 443**  |
| **Configuration push**      | CAPWAP   | **UDP 5246** |
| **Event logs & statistics** | CAPWAP   | **UDP 5246** |
| **Data channel**            | CAPWAP   | **UDP 5247** |

> ⚠️ **Trainer Warning:** Your firewall or router must allow **outbound** connections on TCP 443, UDP 5246, and UDP 5247 from the FortiAP's network to the internet. If any of these are blocked, the AP cannot be managed by FortiLAN Cloud. This is a common deployment issue in environments with strict outbound firewall rules.

---

### Why TCP 443 for Discovery?

Port 443 is the standard **HTTPS port** — almost never blocked by firewalls or ISPs. Fortinet chose it for AP discovery specifically because it ensures FortiAP can reach FortiLAN Cloud even in restrictive network environments (hotels, public networks, etc.).

> 💡 **Real-world analogy:** Using port 443 for discovery is like sending a letter via Priority Mail — it always gets through because everyone knows how to handle it.

---

---

# 📄 PAGE 106 — Provisioning FortiAP — FortiCloud Key

---

## 🖥️ SLIDE SUMMARY

The slide explains the **FortiCloud Key method** for adding FortiAP devices to FortiLAN Cloud:

- **FortiCloud Key** = unique key printed on a **sticker on the physical AP**
- Used to register the AP to your FortiLAN Cloud account
- **Bulk key import** available for adding multiple APs at once (from purchase order)

**GUI path:** `Devices > Inventory Devices > Access Points > Add APs`

Then: `Right-click device > Actions > Deploy`

---

## 📋 DETAILED INSTRUCTOR NOTES

### What Is the FortiCloud Key?

Every FortiAP device ships with a **FortiCloud Key** — a unique alphanumeric code printed on a sticker on the unit (similar to a serial number). This key:

- **Ties the physical hardware** to your FortiCloud account
- Proves ownership of the device to Fortinet's cloud systems
- Is used for **licensing, support entitlement, and cloud management**

> �� **Real-world analogy:** The FortiCloud Key is like the **activation code** on the back of a gift card — you scratch it off and enter it online to link the card to your account.

---

### Step-by-Step: Adding a FortiAP via FortiCloud Key

1. Log in to [`https://fortilan.forticloud.com`](https://fortilan.forticloud.com)
2. Navigate to **Devices → Inventory Devices**
3. Click **Access Points → Add APs**
4. Enter the **FortiCloud Key** from the sticker on the AP
5. The AP is added to your inventory
6. Right-click the AP → **Actions → Deploy**
7. FortiLAN Cloud pushes configuration to the AP when it connects

---

### Bulk Key Import — For Large Deployments

When you purchase **multiple APs at once**, Fortinet provides a **bulk key** on the purchase order document. Instead of entering keys one by one:

- Import the bulk key file directly into FortiLAN Cloud
- All APs are added to inventory simultaneously
- Saves hours of manual data entry for large deployments

> 💡 **Trainer Tip:** Always keep the FortiCloud Key stickers — or photograph them before rack-mounting the APs where the sticker may become inaccessible. Without this key, re-registering the device requires contacting Fortinet support.

---

---

# 📄 PAGE 107 — Provisioning FortiAP — FortiZTP

---

## 🖥️ SLIDE SUMMARY

The slide introduces **FortiZTP** as an alternative provisioning method:

- **FortiZTP** = **Zero-Touch Provisioning** portal by Fortinet
- Provisions FortiAP **directly to FortiLAN Cloud** automatically
- Devices registered to FortiCloud asset management appear automatically in FortiZTP
- After provisioning in FortiZTP → AP appears automatically in FortiLAN Cloud as a **Deployed Device**

**Portal access:** [`https://support.fortinet.com`](https://support.fortinet.com) → Services → FortiZTP

**GUI path in FortiLAN Cloud:** `Devices > Deployed Devices`

---

## 📋 DETAILED INSTRUCTOR NOTES

### What Is FortiZTP?

**FortiZTP (Zero-Touch Provisioning)** is Fortinet's cloud-based automated provisioning system. It is the **most automated** way to deploy FortiAP devices at scale — especially useful when:

- APs are shipped directly to remote sites (no IT on-site)
- Large numbers of APs need to be deployed simultaneously
- You need to avoid manual configuration entirely

---

### How FortiZTP Works — Step by Step

```
1. Purchase FortiAP devices
        ↓
2. Devices are registered to your FortiCloud account
   (either by Fortinet at purchase, or manually via FortiCloud Key)
        ↓
3. Log in to FortiZTP portal (support.fortinet.com → Services → FortiZTP)
        ↓
4. Devices appear automatically in the UNPROVISIONED tab
        ↓
5. Select devices and provision them to FortiLAN Cloud
        ↓
6. Ship APs to remote sites
        ↓
7. Remote user plugs AP into network
        ↓
8. AP boots → contacts FortiLAN Cloud automatically
        ↓
9. FortiLAN Cloud pushes full configuration
        ↓
10. AP appears as DEPLOYED in FortiLAN Cloud dashboard
```

> ✅ **Steps 6–10 require zero technical expertise from the person at the remote site.** They literally just plug it in.

---

### FortiZTP vs. FortiCloud Key — Which to Use?

| Method              | Best For                                               | Technical Effort          |
|---------------------|--------------------------------------------------------|---------------------------|
| **FortiCloud Key**  | Small deployments, manual control, one-by-one addition | Low — just enter the key  |
| **Bulk Key Import** | Medium deployments, multiple APs from one order        | Low — one import file     |
| **FortiZTP**        | Large deployments, remote sites, zero on-site IT       | Minimal — fully automated |

> 💡 **Trainer Tip:** For **enterprise or MSP deployments**, always use **FortiZTP**. It eliminates human error, speeds up deployment, and scales effortlessly. FortiCloud Key entry is fine for labs or small offices but becomes impractical at 50+ APs.

---

### The "UNPROVISIONED" Tab — Key Detail

When you log into FortiZTP, devices appear in the **UNPROVISIONED tab** automatically — but only if they are:

1. Already **registered** to your FortiCloud account (via asset management at `support.fortinet.com`)
2. Using the **same FortiCloud account** as your FortiLAN Cloud

This is why proper **asset registration at the time of purchase** is critical — if a device isn't registered, it won't appear in FortiZTP automatically.

> ⚠️ **Trainer Warning:** A common mistake is purchasing APs and not registering them to your FortiCloud account immediately. Always register devices as soon as they arrive — before sending them to remote sites.

---

---

# 📊 Master Summary — Pages 102–107

```
┌─────────────────────────────────────────────────────────────────────┐
│              FortiLAN Cloud — Complete Picture                      │
├─────────────────┬───────────────────────────────────────────────────┤
│ LICENSING       │ Free: 30 APs, 3 switches, 3 sites, 7-day logs    │
│                 │ Paid: Advanced mgmt, Premium, Multi-tenancy       │
├─────────────────┼───────────────────────────────────────────────────┤
│ KEY BENEFITS    │ Zero-touch, Single pane, Scalable,                │
│                 │ Multi-tenant, Security at edge (FortiAP-U)        │
├─────────────────┼───────────────────────────────────────────────────┤
│ FEATURES        │ Guest portal, RADIUS, Protection profiles,        │
│                 │ Scheduled upgrades, AP maps, Real-time visibility │
├─────────────────┼───────────────────────────────────────────────────┤
│ TRAFFIC FLOW    │ Discovery: HTTPS TCP 443                          │
│                 │ Config/Logs: CAPWAP UDP 5246                      │
│                 │ Data: CAPWAP UDP 5247                             │
│                 │ ⚠️ User traffic = ALWAYS bridged locally          │
├─────────────────┼───────────────────────────────────────────────────┤
│ PROVISIONING    │ FortiCloud Key → manual, one-by-one              │
│ METHODS         │ Bulk Key Import → multiple APs from PO           │
│                 │ FortiZTP → fully automated, zero-touch            │
└─────────────────┴───────────────────────────────────────────────────┘
```

### Topic: Wireless Security — SSID Types & Traffic Modes

---

---

# 📄 PAGE 114 — Types of SSID

---

## 🖥️ SLIDE SUMMARY

The slide introduces the **three SSID types** configurable on FortiGate:

| SSID Type            | Traffic Behavior                                            |
|----------------------|-------------------------------------------------------------|
| 🔵 **Tunnel Mode**   | Traffic tunneled back to FortiGate via CAPWAP data channel  |
| 🟢 **Bridge Mode**   | Traffic bridged directly to the local LAN at the AP         |
| 📡 **Wireless Mesh** | Used as a **backhaul SSID** in distributed/mesh deployments |

**GUI Path:** `WiFi & Switch Controller > SSIDs`

> ⚠️ **Critical Note from slide:** Traffic mode **must be defined at the time of initial SSID configuration** — it cannot be changed later without deleting and recreating the SSID.

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Does SSID Type Matter So Much?

The SSID type determines **how every single packet from a wireless client travels through your network**. This is one of the most fundamental design decisions in a Fortinet wireless deployment — it affects:

- **Security posture** — who inspects the traffic?
- **Performance** — where does traffic processing happen?
- **Network design** — what VLANs, subnets, and policies are needed?
- **Scalability** — how much load is placed on FortiGate?

> 💡 **Real-world analogy:** Choosing an SSID mode is like choosing a postal delivery method:
>
> - **Tunnel Mode** = every letter goes through a central sorting facility (FortiGate) before reaching its destination — fully inspected
> - **Bridge Mode** = letters are delivered directly by the local postman — faster, but less centrally controlled
> - **Mesh Mode** = a relay system where letters are passed between post offices before reaching the main facility

---

### The Irreversible Decision — Why It Matters

The slide explicitly states: **"Traffic mode must be defined at the time of initial SSID configuration."**

This means:

- Once you create a **Tunnel Mode** SSID, you **cannot** convert it to Bridge Mode later
- You must **delete the SSID** and recreate it in the correct mode
- All firewall policies, DHCP settings, and AP profiles referencing that SSID would need to be **reconfigured**

> __⚠️ ‼️ **Trainer Warning:** This is a costly mistake in production environments. Always confirm the traffic mode requirement with your network architect **before** deploying. Changing it later means downtime and reconfiguration.‼️__

---

---

# 📄 PAGE 115 — Tunnel Mode

---

## 🖥️ SLIDE SUMMARY

The slide illustrates **Tunnel Mode** — the default SSID mode in FortiGate:

```
Wireless Client
      ↓ (Wi-Fi)
   FortiAP
      ↓ CAPWAP Tunnel (UDP 5247)
   FortiGate ←→ FortiGuard Services
      ↓
   Internet / LAN
```

**Key bullet points:**

- **Default** SSID mode when creating an SSID on FortiGate
- Wireless traffic tunneled via **CAPWAP data channel**
- Each SSID gets a **separate interface** and **dedicated subnet**
- Requires a **separate firewall policy** per SSID subnet

**Pros:**

- ✅ Central place to **enforce security**
- ✅ **Layer 3 traffic segmentation**

**Cons:**

- ❌ FortiGate must be **sized** to handle all wireless traffic
- ❌ If controller goes down → **wireless network goes down**

---

## �� DETAILED INSTRUCTOR NOTES

### How Tunnel Mode Works — Technical Deep Dive

When a wireless client connects to a Tunnel Mode SSID:

1. Client sends a packet over Wi-Fi to FortiAP
2. FortiAP **encapsulates** the packet inside a **CAPWAP tunnel** (UDP 5247)
3. The encapsulated packet travels over the wired network to **FortiGate**
4. FortiGate **decapsulates** the packet and applies:
   - **Firewall policy** — is this traffic allowed?
   - **Security profiles** — antivirus, IPS, web filter, application control
   - **UTM inspection** — deep packet inspection
5. If permitted → packet is forwarded to its destination (LAN or internet)
6. Return traffic follows the same path in reverse

> 💡 **Real-world analogy:** Tunnel Mode is like every employee's work traffic going through a **security checkpoint** at HQ before reaching its destination — nothing gets through without being inspected.

---

### CAPWAP DTLS Encryption

The tunnel uses **DTLS (Datagram TLS)** — TLS adapted for UDP:

- All traffic inside the CAPWAP tunnel is **encrypted**
- Protects client data from eavesdropping on the wired network between AP and FortiGate
- Critical when FortiAP is in a public or untrusted segment

**Two tunnel options:**

| Option       | Encryption      | Use Case                                       |
|--------------|-----------------|------------------------------------------------|
| **DTLS**     | ✅ Encrypted     | Default — production environments              |
| **Non-DTLS** | ❌ No encryption | Only for performance-critical trusted networks |

---

### Separate Interface Per SSID — Why?

In Tunnel Mode, **each SSID appears as a separate Layer 3 interface** on FortiGate:

- FortiGate assigns each SSID its **own IP subnet**
- Each SSID needs its **own DHCP server** configuration
- Each SSID needs its **own firewall policy**

**Example:**

```
SSID "Corporate"  → Interface: wlan1 → Subnet: 10.10.1.0/24
SSID "Guest"      → Interface: wlan2 → Subnet: 10.10.2.0/24
SSID "IoT"        → Interface: wlan3 → Subnet: 10.10.3.0/24
```

This provides clean **Layer 3 traffic segmentation** — Guest clients physically cannot reach Corporate resources at the IP level.

---

### The Two Critical Cons — Explained

**__Con 1: FortiGate sizing__**\_\_ ALL wireless traffic flows through FortiGate in Tunnel Mode:\_\_

- __‼️__ 100 wireless clients × 10 Mbps each = **1 Gbps** through FortiGate_\_‼️\_\_
- FortiGate's **throughput rating** must accommodate this
- Under-sized FortiGate = bottleneck for entire wireless network

**Con 2: Single point of failure** If FortiGate reboots, crashes, or loses power:

- CAPWAP tunnels drop
- All Tunnel Mode SSIDs go offline
- Wireless clients disconnect immediately

> ⚠️ **Trainer Warning:** For mission-critical wireless networks, consider **FortiGate HA (High Availability)** clustering to eliminate this single point of failure.

---

---

# 📄 PAGE 116 — Tunnel Mode — SSID Interface

---

## 🖥️ SLIDE SUMMARY

The slide shows the FortiGate GUI for a **Tunnel Mode SSID interface configuration**:

- Tunneled SSID is treated as a **Layer 3 interface**
- Can configure **administrative access** to the wireless interface
- Must configure **separate DHCP and DNS settings** per SSID

**GUI Path:** `WiFi & Switch Controller > SSIDs`

---

## 📋 DETAILED INSTRUCTOR NOTES

### The SSID as a FortiGate Interface

When you create a Tunnel Mode SSID, FortiGate automatically creates a **virtual network interface** for it. This interface behaves identically to a physical Ethernet interface:

| Setting                   | What It Controls                                            |
|---------------------------|-------------------------------------------------------------|
| **IP Address / Subnet**   | The gateway IP wireless clients will use                    |
| **Administrative Access** | HTTPS, SSH, PING — which services the interface responds to |
| **DHCP Server**           | Assigns IP addresses to wireless clients on this SSID       |
| **DNS Server**            | DNS resolution for clients on this SSID                     |
| **Role**                  | LAN (for trusted SSIDs) or DMZ (for guest SSIDs)            |

---

### Administrative Access — Security Best Practice

You can enable or disable management protocols on the wireless interface:

- **PING** — useful for testing client connectivity
- **HTTPS/SSH** — allows admins to manage FortiGate from the wireless network

> ⚠️ **Security Best Practice:** For **Guest SSIDs**, disable ALL administrative access. You do not want guest clients being able to ping or reach FortiGate's management interface.

---

### DHCP Configuration per SSID

Each Tunnel Mode SSID needs its own **DHCP scope**:

```
SSID "Corporate" DHCP:
  Range: 10.10.1.10 - 10.10.1.254
  Gateway: 10.10.1.1 (FortiGate interface IP)
  DNS: 8.8.8.8

SSID "Guest" DHCP:
  Range: 10.10.2.10 - 10.10.2.254
  Gateway: 10.10.2.1 (FortiGate interface IP)
  DNS: 8.8.4.4
```

> 💡 **Trainer Tip:** Size your DHCP pool based on the **maximum expected concurrent clients** on that SSID — not the total number of employees. Most wireless deployments see 60–80% concurrency at peak.

---

---

# 📄 PAGE 117 — Bridge Mode

---

## 🖥️ SLIDE SUMMARY

The slide illustrates **Bridge Mode** — where wireless traffic stays local:

```
Wireless Client
      ↓ (Wi-Fi)
   FortiAP ←→ Local Network (bridged at L2)
      ↕ CAPWAP Control only (UDP 5246)
   FortiGate (management only)
```

**Key bullet points:**

- SSID operates at **Layer 2** — traffic bridged to FortiAP management subnet
- Wireless and wired stations can share the **same broadcast domain**
- Best practice: use a **VLAN** for wireless clients
- Clients subject to **same firewall policies** as the management subnet

**Pros:**

- ✅ Wired and wireless in **same broadcast domain**
- ✅ Potential **1 Gbps+** LAN throughput per FortiAP (with link aggregation)
- ✅ Certain FortiAP models can perform **local security inspection**

**Cons:**

- ❌ **Limited VLAN pooling** options

---

## 📋 DETAILED INSTRUCTOR NOTES

### How Bridge Mode Works — Technical Deep Dive

In Bridge Mode:

1. Wireless client connects to the AP via Wi-Fi
2. FortiAP **bridges** the client's traffic directly onto the **local wired network**
3. The traffic behaves as if the client is **plugged into the local switch** via Ethernet
4. Only **CAPWAP control traffic** (UDP 5246) goes back to FortiGate
5. **User data traffic** NEVER touches FortiGate

> 💡 **Real-world analogy:** Bridge Mode is like giving wireless clients a **virtual Ethernet cable** plugged directly into the local switch. Traffic flows locally — FortiGate only manages the AP, not the data.

---

### Tunnel Mode vs. Bridge Mode — Side-by-Side Comparison

| Feature                       | Tunnel Mode                       | Bridge Mode                           |
|-------------------------------|-----------------------------------|---------------------------------------|
| **User traffic path**         | FortiAP → FortiGate → Destination | FortiAP → Local network → Destination |
| **FortiGate processes data?** | ✅ Yes — all traffic               | ❌ No — control only                   |
| **Security inspection**       | FortiGate inspects all traffic    | Local AP inspects (if supported)      |
| **Subnet per SSID**           | ✅ Yes — separate                  | ❌ No — shares local subnet            |
| **Best for**                  | Security-first environments       | High-throughput local deployments     |
| **WAN/Remote sites**          | ❌ High WAN bandwidth consumption  | ✅ Traffic stays local — efficient     |
| **Single point of failure**   | ✅ Yes — FortiGate                 | ❌ No — AP works independently         |

---

### When to Use Bridge Mode

Bridge Mode is ideal when:

- **Remote branch** with a WAN link — you don't want ALL traffic backhauled to HQ
- **High-throughput** local applications — video editing, large file transfers
- **IoT devices** that need to be on the same local subnet as their controller
- The **FortiGate's throughput capacity** would be exceeded in Tunnel Mode

---

### VLAN Best Practice in Bridge Mode

The slide states: *"As a best practice, wireless clients must use a VLAN to pass local and internet traffic."*

Without a VLAN in Bridge Mode:

- Wireless clients join the **same subnet as the FortiAP management interface**
- Wireless clients could potentially reach FortiAP management — security risk
- No separation between wired and wireless traffic

With a VLAN:

```
Bridge Mode SSID "Corporate" → VLAN 10 → Switch
Bridge Mode SSID "Guest"     → VLAN 20 → Switch
FortiAP Management            → VLAN 1  → Switch
```

Each SSID maps to its own VLAN — clean separation even in Bridge Mode.

---

### 1 Gbps+ Throughput — Link Aggregation

The slide mentions **"Potential 1 Gbps or more (if using link aggregation on supported APs)"**:

- Some FortiAP models support **LAG (Link Aggregation Group)** — combining multiple Ethernet ports into one logical high-speed uplink
- Two 1 Gbps ports → **2 Gbps** effective uplink
- In Bridge Mode, this full throughput is available to wireless clients
- In Tunnel Mode, the bottleneck is FortiGate's processing capacity, not the AP's uplink

---

---

# 📄 PAGE 118 — Bridge Mode — Security Profile Support

---

## 🖥️ SLIDE SUMMARY

The slide explains that **certain FortiAP models** can perform **local security inspection** in Bridge Mode:

```
[Remote Offices 1-4]
    Each has a FortiAP (Bridge Mode)
         ↓ Local security inspection
    Traffic inspected AT THE AP
         ↓ Only clean traffic forwarded
    Centralized FortiGate (management only)
```

**Supported security profiles (at the AP level):**

- 🦠 **Antivirus** (including botnet protection)
- 🛡️ **IPS** (Intrusion Prevention System)
- 🌐 **Web Filtering**
- 📱 **Application Control**

**Important constraints:**

- ⚠️ Only available in **Bridge Mode** (NOT Tunnel Mode)
- ⚠️ Only on **specific FortiAP models**
- ⚠️ Requires a **separate FortiGuard subscription per AP**

---

## �� DETAILED INSTRUCTOR NOTES

### Why Local Security Inspection Matters

In a standard Bridge Mode deployment, traffic exits locally at the AP — FortiGate never sees it. This creates a **security gap**:

- A malware-infected laptop on the Guest SSID could communicate with a botnet
- A user could access malicious websites
- No IPS to block exploit attempts

**Bridge Mode Security Profiles solve this** by moving the inspection engine **into the AP itself**:

- The AP inspects traffic **before** forwarding it to the local network
- Threats are blocked at the edge — never reach the wired network
- FortiGate remains the management plane only

> 💡 **Real-world analogy:** Instead of sending all packages to a central inspection facility, Bridge Mode Security Profiles are like having a **security scanner at each building's entrance** — threats are caught at the door, not after they've already entered.

---

### The FortiGuard Subscription Requirement

FortiGuard delivers **real-time threat intelligence** to Fortinet security features:

- Antivirus signature updates
- IPS signature updates
- Web filter category database
- Application control database

In Tunnel Mode, **one FortiGate FortiGuard subscription** covers all wireless clients.

In Bridge Mode Security Profiles, **each FortiAP needs its own FortiGuard subscription** — because the AP is doing the inspection independently.

> __⚠️ **Cost Consideration:** If you have 50 remote branch APs using Bridge Mode Security Profiles, you need **50 individual FortiGuard subscriptions** — factor this into your total cost of ownership (TCO) calculation.__

---

### Which FortiAP Models Support This?

Not all FortiAP models have the processing power for security inspection. Supported models are typically:

- **FortiAP-U series** (specifically designed for UTP/security at the edge)
- Higher-end FortiAP models with sufficient CPU and memory

> 💡 **Trainer Tip:** Always check the Fortinet **compatibility matrix** for your specific FortiAP model before planning Bridge Mode security inspection. Not all models listed in the catalog support this feature.

---

---

# 📄 PAGE 119 — Bridge Mode — Security Profile Configuration

---

## 🖥️ SLIDE SUMMARY

The slide shows the **two-step GUI workflow** to configure Bridge Mode security profiles:

**Step 1:** Create the security profile `Security Profiles > Web Filter` (or Antivirus, IPS, App Control)

**Step 2:** Apply the security profile to the SSID `WiFi & Switch Controller > SSIDs` → select the Bridge Mode SSID → apply profile

> 📌 Supported FortiAPs receive **security profile updates automatically from FortiGuard** via their subscription.

---

## 📋 DETAILED INSTRUCTOR NOTES

### Configuration Workflow — Detailed Steps

**Step 1: Create the Security Profile** Navigate to `Security Profiles` and create profiles for each service you want:

- **Web Filter** — define allowed/blocked URL categories
- **Antivirus** — configure scan settings and botnet detection
- **IPS** — choose signature sets (default, all-threats, or custom)
- **Application Control** — define allowed/blocked application categories

**Step 2: Apply to the SSID**

- Go to `WiFi & Switch Controller > SSIDs`
- Edit the **Bridge Mode SSID**
- In the security profile section, assign the profiles you created

**Step 3: FortiAP Receives Updates**

- The FortiAP connects to **FortiGuard** using its own subscription
- Signature updates are downloaded **directly to the AP** — not through FortiGate
- Updates happen automatically on a schedule

---

### Important Operational Note

The slide states: *"Supported FortiAPs get security profile updates from FortiGuard through a FortiGuard subscription."*

This means the FortiAP must have:

1. **Internet access** — to reach FortiGuard update servers
2. A **valid FortiGuard subscription** registered to that specific AP's serial number
3. Correct **DNS resolution** to reach FortiGuard FQDNs

> ⚠️ **Trainer Warning:** If a remote branch FortiAP loses internet connectivity, it cannot receive FortiGuard updates. Threat signatures will become stale over time — reducing detection effectiveness. Monitor subscription and connectivity status regularly.

---

---

# 📄 PAGE 120 — Wireless Mesh SSID

---

## 🖥️ SLIDE SUMMARY

The slide explains **Mesh Mode SSID** — the third SSID type:

**Minimum devices required for mesh:**

1. **FortiGate** — wireless controller
2. **FortiAP** — mesh root AP (wired to FortiGate)
3. **FortiAP** — leaf AP (connects wirelessly via backhaul SSID)

**Key concept:**

- The **Backhaul SSID** is used exclusively for **AP-to-AP wireless connectivity**
- Regular clients (laptops, phones) do NOT connect to the mesh SSID
- Select **"Mesh"** for the traffic mode when creating this SSID type

**GUI Path:** `WiFi & Switch Controller > SSIDs`

---

## 📋 DETAILED INSTRUCTOR NOTES

### What Mesh Mode SSID Actually Does

A Mesh Mode SSID is **not for end users** — it is an AP management and backhaul SSID:

- It carries **CAPWAP control traffic** (configuration, management) wirelessly between APs
- It carries **encapsulated user traffic** from leaf APs back to the root AP
- It replaces the **Ethernet cable** that would normally connect each AP to the network

> 💡 **Real-world analogy:** The Mesh SSID is like an **internal company radio frequency** — only company vehicles (APs) use it to communicate with dispatch (FortiGate). Regular customers (clients) use different frequencies (client SSIDs).

---

### When Is Mesh Mode Used?

The slide describes the classic use case:

> *"An outbuilding with power but no Ethernet to the main building could have a FortiAP in mesh mode connected to a root FortiAP that does have an Ethernet connection to the main network."*

Real-world examples:

- **Warehouse** connected to main office building — no cable run
- **Parking lot kiosk** or **outdoor event tent**
- **Historic building** where drilling cable conduits is prohibited
- **Temporary deployments** where permanent cabling isn't justified

---

### How Mesh Bridges Client Traffic

The mesh AP runs **two SSIDs simultaneously**:

| Radio   | SSID Type                       | Purpose               |
|---------|---------------------------------|-----------------------|
| 5 GHz   | **Mesh Backhaul SSID**          | AP-to-AP traffic only |
| 2.4 GHz | **Client SSID** (Tunnel/Bridge) | End-user devices      |

Client traffic flow in Mesh:

```
Client → Leaf AP (client SSID)
       → Leaf AP bridges to Mesh backhaul SSID
       → Root AP receives traffic via backhaul
       → Root AP sends traffic via Ethernet to FortiGate
```

---

---

# 📄 PAGE 121 — Wireless Mesh — Configuration

---

## 🖥️ SLIDE SUMMARY

The slide outlines the **step-by-step configuration** for setting up a wireless mesh:

**Configuration Steps:**

1. Create a **Mesh SSID** on FortiGate (`WiFi & Switch Controller > SSIDs`)
2. Broadcast the mesh SSID using the **AP profile** associated with the **root AP** (`WiFi & Switch Controller > FortiAP Profiles`)
3. Access the **downstream (leaf) AP** via HTTPS or SSH
4. Change the leaf AP's **uplink connectivity from Ethernet to Mesh**
5. Enter the **mesh SSID credentials** on the downstream AP

**CLI Commands on the leaf FortiAP (via SSH):**

```bash
cfg -a MESH_AP_TYPE=1
cfg -a MESH_AP_SSID=Mesh Uplink
cfg -a MESH_AP_PASSWD=test1234
cfg -c
exit
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### CLI Commands Explained Line by Line

```bash
cfg -a MESH_AP_TYPE=1
```

- Sets this AP's role to **mesh leaf/branch** (type 1)
- Type 0 = normal wired uplink; Type 1 = mesh wireless uplink

```bash
cfg -a MESH_AP_SSID=Mesh Uplink
```

- Tells the AP the **name (SSID)** of the backhaul network to connect to
- Must exactly match the Mesh SSID name configured on FortiGate

```bash
cfg -a MESH_AP_PASSWD=test1234
```

- The **password/PSK** for the mesh SSID
- Must match the security settings of the backhaul SSID on FortiGate
- ⚠️ Use a **strong password** in production — this protects AP-to-AP communication

```bash
cfg -c
```

- **Commits** the configuration changes to memory
- Without this command, changes are lost on reboot

```bash
exit
```

- Exits the SSH session
- AP will reboot and attempt to join the mesh network

> ⚠️ **Trainer Warning:** `cfg -c` is critical — a common mistake is forgetting to commit, then being confused when the AP reboots without the mesh config. Always verify with `cfg -e` (export/show config) after committing.

---

### Why the Two-Step GUI + CLI Process?

The root AP is configured **entirely from FortiGate GUI** (single pane of glass). But the leaf AP:

- Is not yet managed by FortiGate (it has no wired connection to reach FortiGate)
- Must be configured **locally** via its own HTTPS/SSH interface
- Once it joins the mesh and reaches FortiGate via the backhaul → FortiGate takes over management

> 💡 **Trainer Tip:** Connect a laptop directly to the leaf AP's LAN port via Ethernet for initial SSH/HTTPS access. Once the mesh is configured and working, the AP is fully managed by FortiGate and you never need local access again.

---

---

# 📄 PAGE 122 — Wireless Mesh — Configuration (Continued)

---

## 🖥️ SLIDE SUMMARY

The slide shows the **FortiGate GUI for authorizing downstream (leaf) APs** in a mesh topology:

**GUI Path:** `WiFi & Switch Controller > Managed FortiAPs`

- After leaf AP connects via the mesh backhaul → it appears in the **Managed FortiAPs** list
- You must **authorize** the leaf AP just like any other FortiAP
- Click the **"+" beside the root FortiAP** to expand and view all downstream APs associated with it

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Authorization Step — Why It's Required

Even though the leaf AP connects wirelessly through the mesh, it still must go through the **standard FortiAP authorization process**:

1. Leaf AP discovers FortiGate via the mesh backhaul
2. Leaf AP sends a **CAPWAP Join Request** to FortiGate
3. FortiGate lists the leaf AP as **"pending authorization"** in Managed FortiAPs
4. Admin must right-click and select **Authorize**
5. FortiGate pushes the full AP profile and SSID configuration to the leaf AP

> 💡 **Security Reason:** The authorization requirement prevents unauthorized APs from joining your mesh network and gaining network access. Even if someone knows your mesh SSID password, FortiGate will not push configuration to unauthorized APs.

---

### Viewing the Mesh Hierarchy in GUI

The GUI shows the **mesh topology as a tree structure**:

```
Root FortiAP (wired)          ← expand with "+"
  └── Branch FortiAP (mesh)
        └── Leaf FortiAP (mesh)
```

- Click **"+"** beside the root AP to expand all downstream APs
- Each AP shows its **signal strength, status, and uplink quality**
- This visual hierarchy makes it easy to identify **weak links** in the mesh chain

---

### Repeat for Each Additional Downstream AP

The instructor notes state: *"You must repeat the same process for any additional downstream FortiAP."*

This means for each additional leaf AP:

1. Physically access it via HTTPS/SSH
2. Run the same `cfg -a` commands with correct SSID and password
3. Commit with `cfg -c`
4. Return to FortiGate GUI and authorize it

> ⚠️ **Trainer Warning:** In large mesh deployments with many leaf APs, this manual process can be time-consuming. Plan the deployment carefully — pre-configure as many APs as possible in the lab before shipping them to site.

---

---

# 📊 Master Summary — Pages 114–122

```
┌─────────────────────────────────────────────────────────────────────┐
│           SSID TYPES — COMPLETE COMPARISON                         │
├──────────────┬──────────────────────┬──────────────────────────────┤
│ Feature      │ TUNNEL MODE          │ BRIDGE MODE                  │
├──────────────┼──────────────────────┼──────────────────────────────┤
│ Default?     │ ✅ YES               │ ❌ No                        │
│ Data path    │ All traffic→FortiGate│ Traffic stays local at AP    │
│ Inspection   │ FortiGate inspects   │ AP inspects (if supported)   │
│ Interface    │ Separate L3 per SSID │ Shares local subnet          │
│ DHCP         │ Per-SSID DHCP needed │ Uses existing DHCP           │
│ Throughput   │ Limited by FortiGate │ Up to 1Gbps+ per AP          │
│ Best for     │ Security-first       │ Remote sites, high traffic   │
│ Failure risk │ FortiGate = SPOF     │ AP works independently       │
├──────────────┴──────────────────────┴──────────────────────────────┤
│                      MESH MODE                                      │
├─────────────────────────────────────────────────────────────────────┤
│ Purpose: Backhaul SSID — AP-to-AP connectivity (not for clients)   │
│ Minimum: FortiGate + Root FortiAP + Leaf FortiAP                   │
│ Best for: Areas where Ethernet cabling is impossible               │
│ Config: GUI for root AP, SSH CLI for leaf AP                       │
│ CLI: cfg -a MESH_AP_TYPE=1 / SSID / PASSWD → cfg -c              │
│ Auth: Leaf APs must be authorized in Managed FortiAPs GUI          │
└─────────────────────────────────────────────────────────────────────┘

⚠️  REMEMBER: Traffic mode CANNOT be changed after SSID creation!
```

### Topic: Wireless Security — Authentication Protocols & Encryption

---

---

# 📄 PAGE 126 — 802.1X Overview

---

## 🖥️ SLIDE SUMMARY

The slide introduces **IEEE 802.1X** — the standard for **Layer 2 network authentication**:

**Key definitions:**

- Provides **device Layer 2 authentication** before network access is granted
- Defines the encapsulation of **EAP (Extensible Authentication Protocol)**
- Acts as an **authentication framework** for transporting user credentials

**Three players (the 802.1X triangle):**

| Role                         | Who It Is                               | What It Does                                        |
|------------------------------|-----------------------------------------|-----------------------------------------------------|
| 🖥️ **Supplicant**           | The client device (laptop, phone)       | Wants to join the network — provides credentials    |
| 📡 **Authenticator**         | The wireless AP or switch               | Forwards credentials — does NOT make auth decisions |
| 🔐 **Authentication Server** | RADIUS server (e.g. FortiAuthenticator) | Validates credentials — grants or denies access     |

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why 802.1X Exists — The Problem It Solves

Before 802.1X, wireless networks used **Pre-Shared Keys (PSK)**:

- One password shared by everyone
- If one person leaves the company → you must change the password for **everyone**
- No per-user identity — you can't tell WHO connected, only THAT someone connected
- No per-user access control — everyone gets the same network access

**802.1X solves all of this:**

- Every user has **unique credentials** (username/password or certificate)
- Access is granted **per identity** — you know exactly who is on the network
- When someone leaves → just disable their account in Active Directory
- Different users can get **different levels of access** based on identity (RBAC)

> 💡 **Real-world analogy:** PSK is like giving everyone the same key to the office. 802.1X is like giving everyone a **personal badge** — each person is individually identified, access is logged, and revoking one person's access doesn't affect anyone else.

---

### The Three Players — Deep Dive

#### 🖥️ Supplicant

- Software running on the **client device**
- Built into Windows, macOS, iOS, Android natively
- Responsible for:
  - Presenting credentials to the authenticator
  - Performing the selected EAP method
  - Establishing the encrypted tunnel if required (PEAP/TLS)

#### 📡 Authenticator (FortiGate / FortiAP)

- The **"middleman"** — critically important concept:
  - The authenticator **does NOT decide** whether to grant access
  - It simply **relays** EAP messages between supplicant and authentication server
  - It does NOT need to know the EAP method being used
  - It does NOT need certificates (in most deployments)
- Controls the **"controlled port"** — blocks all traffic until authentication succeeds
- After successful auth → **opens the port** and grants network access

#### 🔐 Authentication Server (FortiAuthenticator / RADIUS)

- The **brain** of 802.1X — makes all authentication decisions
- Supports **RADIUS protocol** (Remote Authentication Dial-In User Service)
- Checks credentials against:
  - Local user database
  - Active Directory / LDAP
  - Certificate Authority (for EAP-TLS)
- Returns **Access-Accept** (grant) or **Access-Reject** (deny) to the authenticator

> ⚠️ **Critical Concept:** The authenticator (FortiGate/FortiAP) is **transparent to the authentication process**. It simply passes EAP frames between client and server. This is why you don't need to configure EAP methods on FortiGate — only on the RADIUS server and the client.

---

### The 802.1X Controlled Port Concept

Before authentication:

- The switch/AP port is in **"uncontrolled"** state
- Only **EAPOL (EAP over LAN)** frames can pass — no regular traffic
- Client has NO network access

After successful authentication:

- The port switches to **"controlled"** state
- All traffic is now permitted (subject to firewall policies)
- Client has full network access

> 💡 **Real-world analogy:** Think of the controlled port like a **turnstile at a subway station** — you can't pass through until you tap your card. Once authenticated, the gate opens.

---

---

# 📄 PAGE 127 — EAP (Extensible Authentication Protocol)

---

## 🖥️ SLIDE SUMMARY

The slide introduces **EAP** and its various types:

**EAP Background:**

- Evolved from **PPP (Point-to-Point Protocol)**
- Originally used for **wired network authentication**
- EAP itself is **unencrypted** — security comes from the specific EAP method used

**EAP Types (from least to most secure):**

| EAP Type       | Full Name                      | Notes                                                |
|----------------|--------------------------------|------------------------------------------------------|
| **Cisco LEAP** | Lightweight EAP                | Proprietary, Cisco-only, **deprecated** (vulnerable) |
| **EAP-TLS**    | EAP-Transport Layer Security   | Most secure — certificates on both sides             |
| **PEAP**       | Protected EAP                  | **Most widely used** — server cert only              |
| **EAP-TTLS**   | EAP-Tunneled TLS               | Flexible — supports legacy auth inside               |
| **EAP-SIM**    | EAP-Subscriber Identity Module | For cellular/SIM card authentication                 |

---

## 📋 DETAILED INSTRUCTOR NOTES

### EAP Is Not a Wire Protocol — Critical Concept

The slide states: *"EAP is not a wire protocol; instead it only defines message formats."*

What this means:

- EAP defines **what messages look like** but not how they travel
- Different network types encapsulate EAP differently:
  - **EAPOL** = EAP over LAN (used between supplicant and authenticator on Ethernet/Wi-Fi)
  - **EAP over RADIUS** = EAP messages carried inside RADIUS packets (between authenticator and auth server)
- The authenticator **unwraps EAPOL** and **re-wraps into RADIUS** — transparent relay

---

### EAP Types — Detailed Comparison

#### ❌ Cisco LEAP — Avoid in Production

- Cisco proprietary — works only on Cisco infrastructure
- **Vulnerable to offline dictionary attacks** (ASLEAP attack tool)
- **Deprecated** — do not use in new deployments

#### 🔒 EAP-TLS — Most Secure

- Requires **certificates on BOTH client and server**
- Mutual authentication — client verifies server AND server verifies client
- Strongest security but **hardest to deploy** (every device needs a cert)
- See Page 129 for deep dive

#### ✅ PEAP — Most Common in Enterprise

- Only **server needs a certificate** — much easier to deploy
- Creates a TLS tunnel first, then authenticates the client inside
- Most client devices support it natively (Windows, macOS, iOS, Android)
- See Page 128 for deep dive

#### 🔄 EAP-TTLS — Flexible but Less Common

- Similar to PEAP but supports **legacy authentication** (PAP, CHAP) inside the tunnel
- Useful when migrating from older auth systems
- May require **third-party supplicant** software on Windows
- See Page 130 for deep dive

#### 📱 EAP-SIM — Mobile/Carrier Use

- Uses the **SIM card** as the authentication credential
- Common in **carrier Wi-Fi offload** scenarios (hotspots authenticating mobile subscribers)
- Not typically used in enterprise WLAN deployments

---

### One-Way vs. Mutual Authentication

| Authentication Type | Who is Verified                 | EAP Methods                               |
|---------------------|---------------------------------|-------------------------------------------|
| **One-way**         | Only the client is verified     | LEAP, some PEAP configs                   |
| **Mutual**          | Both client AND server verified | EAP-TLS, PEAP with server cert validation |

> ⚠️ **Security Best Practice:** Always use **mutual authentication** — clients should verify the server certificate to prevent **rogue AP / evil twin attacks** where an attacker sets up a fake RADIUS server to steal credentials.

---

---

# 📄 PAGE 128 — Encapsulated EAP Methods (PEAP)

---

## 🖥️ SLIDE SUMMARY

The slide explains **PEAP (Protected EAP)** — the most widely used EAP method:

**How PEAP works:**

1. A **TLS session** is established first (using server's X.509 certificate)
2. Inside the encrypted TLS tunnel, client authentication occurs using:
   - **PEAPv0/MSCHAPv2** — authenticates client using MS-CHAPv2 (username/password)
   - **PEAPv1/EAP-GTC** — supports one-time passwords and other flexible methods

**Key fact:** Natively supported on **most devices** (Windows, macOS, iOS, Android)

**Flow:**

```
Supplicant (Client) ←→ Authenticator (FortiGate) ←→ Auth Server (FortiAuthenticator)
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### PEAP — Two-Phase Process Explained

**Phase 1 — TLS Tunnel Establishment (Outer Authentication):**

```
Client ←→ FortiAP ←→ FortiAuthenticator
         [Server presents X.509 certificate]
         [Client verifies certificate against trusted CA]
         [TLS tunnel established — all further traffic encrypted]
```

**Phase 2 — Client Authentication (Inner Authentication):**

```
Inside the TLS tunnel:
Client sends username/password (MSCHAPv2)
FortiAuthenticator verifies against AD/LDAP
Access-Accept or Access-Reject returned
```

> 💡 **Real-world analogy:** PEAP is like sending a **sealed envelope inside another sealed envelope**:
>
> - The outer envelope (TLS tunnel) protects the contents from interception
> - The inner envelope (MSCHAPv2) contains the actual credentials
> - Even if someone intercepts the outer envelope, they cannot read what's inside

---

### PEAPv0/MSCHAPv2 — Why It's the Default

**MSCHAPv2 (Microsoft Challenge Handshake Authentication Protocol v2):**

- Microsoft's challenge-response authentication method
- Integrates natively with **Active Directory** — no extra infrastructure needed
- Supported natively on ALL Windows devices
- How it works:
  1. Server sends a **random challenge** to the client
  2. Client hashes the challenge + password using **NT hash**
  3. Server verifies the hash against Active Directory
  4. No plaintext password ever transmitted — even inside the TLS tunnel

> ⚠️ **Security Note:** MSCHAPv2 itself (without PEAP) is vulnerable to offline attacks. But inside a PEAP TLS tunnel, it is safe because the challenge-response exchange is encrypted.

---

### PEAPv1/EAP-GTC — When to Use It

**EAP-GTC (Generic Token Card):**

- Designed for **one-time password (OTP)** systems (RSA SecurID, Google Authenticator, etc.)
- Very flexible — supports any identification type
- **Not natively supported** on most Windows devices — requires 3rd party supplicant
- **FortiAuthenticator supports it** — useful when OTP is a requirement

> 💡 **Trainer Tip:** Use PEAPv0/MSCHAPv2 for standard enterprise deployments with Active Directory. Use PEAPv1/EAP-GTC only when OTP or advanced authentication is specifically required.

---

---

# 📄 PAGE 129 — Encapsulated EAP Methods (EAP-TLS)

---

## 🖥️ SLIDE SUMMARY

The slide covers **EAP-TLS** — the most secure EAP method:

**Key characteristics:**

- The **original, standard** EAP authentication protocol
- Requires **certificates on BOTH client AND server**
- Both parties must have a **valid certificate** installed
- Requires a **PKI (Public Key Infrastructure)** implementation for all wireless clients
- Every device must have a valid certificate to connect to the wireless network

---

## 📋 DETAILED INSTRUCTOR NOTES

### EAP-TLS — How It Works

Unlike PEAP (server cert only), EAP-TLS requires **mutual certificate authentication**:

**Server-side:**

- FortiAuthenticator presents its **server certificate** to the client
- Client verifies the certificate is signed by a **trusted CA**

**Client-side:**

- Each client device has a **unique client certificate** installed
- FortiAuthenticator verifies the client cert is signed by the **same trusted CA**
- No username/password required — the certificate IS the credential

**Authentication flow:**

```
1. Client → AP: "I want to connect"
2. Server → Client: "Here is my certificate [signed by CA]"
3. Client verifies server cert → TLS tunnel established
4. Client → Server: "Here is MY certificate [signed by CA]"
5. Server verifies client cert → Access-Accept
6. Four-way handshake → Client on network
```

---

### Why EAP-TLS Is the Most Secure

| Security Property           | EAP-TLS                 | PEAP/MSCHAPv2          |
|-----------------------------|-------------------------|------------------------|
| **No password transmitted** | ✅ Never                 | ✅ Hash only            |
| **No password to steal**    | ✅ Certificate only      | ❌ Password exists      |
| **Mutual authentication**   | ✅ Always                | ✅ With cert validation |
| **Phishing resistant**      | ✅ Cert can't be phished | ❌ Password can be      |
| **Deployment complexity**   | ❌ High — PKI needed     | ✅ Low — AD integration |

---

### PKI Infrastructure Requirement — The Big Challenge

To deploy EAP-TLS, you need:

1. A **Certificate Authority (CA)** — issues and signs certificates (FortiAuthenticator can be your CA)
2. **Server certificate** — installed on FortiAuthenticator
3. **Client certificates** — one unique cert for **every device** that will connect
4. A **distribution mechanism** — Group Policy (Windows AD), MDM (Intune, Jamf), or manual installation
5. A **certificate revocation** process — when devices are lost/stolen, revoke their cert immediately

> ⚠️ **Trainer Warning:** EAP-TLS is technically superior but operationally demanding. In organizations with hundreds of devices, certificate lifecycle management becomes a full-time task. Many organizations choose PEAP instead for its simpler operational model.

> 💡 **Best of both worlds:** Use **FortiAuthenticator as your CA** + **SCEP (Simple Certificate Enrollment Protocol)** + **MDM** to automate certificate distribution — this makes EAP-TLS much more manageable at scale.

---

---

# 📄 PAGE 130 — Encapsulated EAP Methods (EAP-TTLS)

---

## 🖥️ SLIDE SUMMARY

The slide explains **EAP-TTLS (Tunneled TLS)**:

**How EAP-TTLS works:**

1. A **TLS session** is established first (using server's X.509 certificate)
2. Inside the TLS tunnel, **AVPs (Attribute-Value Pairs)** are exchanged
3. AVPs authenticate the client using:
   - A **native EAP method**, OR
   - A **legacy protocol** (PAP or CHAP)

**Key notes:**

- Client certificate is **optional** (unlike EAP-TLS)
- May require **third-party utility** for wireless authentication on some platforms
- Less commonly supported than PEAP natively

---

## 📋 DETAILED INSTRUCTOR NOTES

### EAP-TTLS vs. PEAP — What's the Difference?

Both PEAP and EAP-TTLS create a TLS tunnel first. The difference is what happens **inside** the tunnel:

| Feature                    | PEAP                             | EAP-TTLS                          |
|----------------------------|----------------------------------|-----------------------------------|
| **Inner auth method**      | EAP methods only (MSCHAPv2, GTC) | EAP methods OR legacy (PAP, CHAP) |
| **Client certificate**     | Not required                     | Optional                          |
| **Native Windows support** | ✅ Yes                            | ❌ Needs 3rd party                 |
| **Legacy system support**  | Limited                          | ✅ Excellent                       |
| **Typical use case**       | Enterprise with AD               | Migration from legacy auth        |

---

### AVPs (Attribute-Value Pairs) — Explained

AVPs are **key-value data structures** used in RADIUS and EAP-TTLS:

```
AVP Example:
  Attribute: User-Name
  Value: john.doe@company.com

  Attribute: User-Password
  Value: [hashed password]
```

In EAP-TTLS, these AVPs are exchanged **inside the encrypted TLS tunnel** — so even though PAP (plaintext password) might be used internally, it's protected by the TLS encryption wrapper.

> ⚠️ **Security Note:** PAP inside EAP-TTLS is acceptable because the TLS tunnel encrypts everything. But PAP without EAP-TTLS would send passwords in plaintext — **never** use bare PAP on wireless networks.

---

### When to Choose EAP-TTLS in Production

- You have **legacy authentication systems** (UNIX/Linux with PAP/CHAP) that can't support MSCHAPv2
- You're migrating from an older auth infrastructure and need backward compatibility
- Your RADIUS server supports EAP-TTLS but your clients are mixed platform (Linux clients often prefer EAP-TTLS)

---

---

# 📄 PAGE 131 — WPA2 Message Handshake

---

## 🖥️ SLIDE SUMMARY

The slide provides an overview of the **WPA2 connection sequence**:

```
Client                          FortiAP
  |                                |
  |←── Authentication Request ────→|
  |←── Authentication Response ───→|
  |←── Association Request ───────→|
  |←── Association Response ──────→|
  |←──── 4-Way Handshake ─────────→|
  |          [Encrypted link]       |
```

**Key concepts:**

- **RSNA (Robust Security Network Association)** — the WPA2 security framework
- Requires mutual authentication + association + four-way handshake
- **Five keys** are created in total
- **Two master keys:**
  - **GMK** — Group Master Key
  - **PMK** — Pairwise Master Key
- PMK is created by either **802.1X/EAP** or **PSK authentication**

> �� **Important note from slide:** 802.1X authentication happens **BEFORE** the WPA2 exchange

---

## 📋 DETAILED INSTRUCTOR NOTES

### WPA2 Connection Sequence — Full Step-by-Step

**Step 1: 802.1X/EAP Authentication (if enterprise)**

- This happens FIRST — before any WPA2 exchange
- Client proves its identity to the RADIUS server
- Result: PMK is derived from the EAP session

**Step 2: Authentication Exchange (802.11 Open System)**

- Client sends **Authentication Request** to AP
- AP responds with **Authentication Response**
- Note: This 802.11 "authentication" is just a formality — not the real security auth

**Step 3: Association**

- Client sends **Association Request** — "I want to join this BSS (Basic Service Set)"
- AP responds with **Association Response** + assigns an **Association ID (AID)**
- Client is now "associated" but not yet exchanging encrypted data

**Step 4: Four-Way Handshake**

- Derives actual **encryption keys** from the PMK
- After this, all data is encrypted
- See Page 132 for full detail

---

### The Five Keys of WPA2/RSNA

```
Layer 1 — Master Keys (long-lived):
  ├── GMK (Group Master Key)    — generated by AP
  └── PMK (Pairwise Master Key) — from EAP or PSK

Layer 2 — Temporal Keys (session-specific):
  ├── PTK (Pairwise Temporal Key)  — unicast encryption
  ├── GTK (Group Temporal Key)     — broadcast/multicast encryption
  └── [Additional integrity keys within PTK]
```

> 💡 **Real-world analogy:** Master keys are like a **master blueprint** — you use them to create specific keys for specific doors (temporal keys). The blueprint itself is never used to open a door directly.

---

### PSK vs. 802.1X — How PMK is Derived

| Method                  | How PMK is Created                                                 |
|-------------------------|--------------------------------------------------------------------|
| **PSK (Personal)**      | PMK = hash of passphrase + SSID name (PBKDF2 with 4096 iterations) |
| **802.1X (Enterprise)** | PMK = derived from EAP session keys (MSK — Master Session Key)     |

> ⚠️ **Security Implication of PSK PMK:** Since PSK PMK is derived from the passphrase, a **captured 4-way handshake** can be subjected to offline dictionary attack to crack the passphrase. This is why WPA3 SAE was introduced (see Page 133).

---

---

# 📄 PAGE 132 — Four-Way Handshake

---

## 🖥️ SLIDE SUMMARY

The slide shows the **detailed four-way handshake** between supplicant and authenticator:

```
Supplicant (Client)              Authenticator (AP)
PMK known                        PMK known + GMK
Generate SNonce                  Generate ANonce

          ←── Message 1: ANonce (Unicast) ──────

Derive PTK from: PMK + ANonce + SNonce + MACs

          ──── Message 2: SNonce + MIC ─────────→

                          Derive PTK (verify MIC)
                          Generate GTK from GMK

          ←── Message 3: Install PTK + Encrypted GTK ──

Install PTK + GTK

          ──── Message 4: Acknowledgement (MIC) ─→

                          Install PTK
                          [Port UNLOCKED — network access granted]
```

**Final keys:**

- **PTK** — encrypts/decrypts **unicast** traffic (no timeout)
- **GTK** — encrypts/decrypts **broadcast and multicast** traffic (changes regularly)

---

## 📋 DETAILED INSTRUCTOR NOTES

### Nonces — What They Are and Why They Matter

**Nonce = Number used ONCE** — a randomly generated value:

- **ANonce** = AP Nonce (generated by the authenticator)
- **SNonce** = Supplicant Nonce (generated by the client)

Why nonces matter:

- They ensure the **PTK is unique for every session** — even if the PMK is the same
- Prevents **replay attacks** — an attacker can't reuse captured handshake frames
- Provides **forward secrecy** — compromising one session's keys doesn't compromise past sessions

---

### PTK Derivation — The Math Behind It

The **PTK (Pairwise Temporal Key)** is derived using a **PRF (Pseudo-Random Function)**:

```
PTK = PRF(PMK, "Pairwise key expansion",
          MIN(AP_MAC, Client_MAC) ||
          MAX(AP_MAC, Client_MAC) ||
          MIN(ANonce, SNonce) ||
          MAX(ANonce, SNonce))
```

**Inputs to PTK derivation:**

| Input                  | Purpose                                        |
|------------------------|------------------------------------------------|
| **PMK**                | The master secret                              |
| **ANonce**             | AP randomness — ensures session uniqueness     |
| **SNonce**             | Client randomness — ensures session uniqueness |
| **AP MAC address**     | Binds key to this specific AP                  |
| **Client MAC address** | Binds key to this specific client              |

> 💡 **Key insight:** Because both MAC addresses are inputs, the PTK is **unique per client-AP pair** — even with the same PMK. Two clients on the same SSID get completely different PTKs.

---

### MIC (Message Integrity Code) — Why Messages 2, 3, 4 Include It

**MIC** = cryptographic integrity check on EAPoL-Key frames:

- Proves the sender **possesses the PTK** without revealing it
- Prevents **man-in-the-middle** attacks during the handshake
- If MIC verification fails → handshake is aborted, connection denied

---

### GTK — Why It Changes Regularly

The **GTK (Group Temporal Key)** encrypts broadcast/multicast traffic sent to ALL clients:

- If GTK never changed, a client who **left the network** could still decrypt broadcast traffic
- By rotating GTK regularly, **ex-clients can no longer decrypt** network broadcast traffic
- GTK rotation is called a **Group Key Handshake** (a separate 2-frame exchange)

---

### Roaming and the Four-Way Handshake

The slide states: *"Each time a client roams from one AP to another, a new four-way handshake occurs."*

This means:

- Client moves from AP1 to AP2 → **full 4-way handshake repeats**
- New ANonce, new SNonce → new PTK
- Brief **authentication delay** during roam
- This is why **fast roaming (802.11r)** was invented — to pre-cache PMKs and accelerate the handshake during roaming

---

---

# 📄 PAGE 133 — WPA3

---

## 🖥️ SLIDE SUMMARY

The slide introduces **WPA3** and the problems it solves:

**WPA3 addresses:**

- ❌ **KRACK vulnerability** (Key Reinstallation Attack) — affected WPA2
- ❌ **Offline dictionary attacks** — WPA2's PSK is vulnerable

**WPA3's solution — SAE (Simultaneous Authentication of Equals):**

- Based on **Dragonfly Key Exchange** (RFC 7664)
- More resilient for **weak passwords**
- Introduces a **secure handshake** that prevents dictionary attacks
- Unlike WPA2, WPA3 **does NOT use the passphrase directly** to derive the PMK

---

## 📋 DETAILED INSTRUCTOR NOTES

### The KRACK Vulnerability — What It Was

**KRACK (Key Reinstallation Attack)** — discovered in 2017:

- Exploited a flaw in the **WPA2 four-way handshake**
- An attacker could **force nonce reuse** by replaying handshake messages
- With repeated nonces, encryption becomes predictable → attacker can decrypt traffic
- **All WPA2 implementations** were vulnerable (Windows, macOS, iOS, Android, Linux)

**Why KRACK worked:**

- WPA2's handshake required the client to **re-install the PTK** if Message 3 was retransmitted
- Attacker retransmits Message 3 → client re-installs key with already-used nonce
- Once nonce is reused → encryption is broken for that session

**WPA3's fix:**

- SAE handshake does not allow nonce reinstallation
- Each SAE exchange generates a **completely fresh PMK** regardless of retransmissions

---

### Offline Dictionary Attack — WPA2's Weakness

In WPA2-Personal (PSK):

1. Attacker captures the **four-way handshake** (easily done with passive sniffing)
2. Takes the captured handshake **offline**
3. Runs dictionary attack: tries millions of passwords, derives PMK, checks if MIC matches
4. If password is in the dictionary → **cracked**

**Tools used by attackers:** hashcat, aircrack-ng, cowpatty

**WPA3's fix with SAE:**

- The passphrase is NOT directly used to derive the PMK
- SAE uses **elliptic curve cryptography (ECC)** to derive PMK from both parties' contributions
- Even with a captured SAE exchange, offline cracking is **computationally infeasible**
- SAE blocks authentication after a **certain number of failed attempts** — prevents brute force

> ⚠️ **Trainer Note:** WPA3 makes **weak passwords safer** — but that doesn't mean you should use weak passwords. Always enforce strong passphrases as a defense-in-depth measure.

---

---

# 📄 PAGE 134 — How Is WPA3 Different?

---

## 🖥️ SLIDE SUMMARY

The slide presents a detailed **WPA2 vs. WPA3 comparison table**:

| Feature                   | WPA2                                    | WPA3                                         |
|---------------------------|-----------------------------------------|----------------------------------------------|
| **Released**              | 2004                                    | 2018                                         |
| **Encryption**            | AES with CCMP                           | AES-GCM + Elliptic Curve (CNSA Suite B)      |
| **Session Key Size**      | **128-bit**                             | **192-bit**                                  |
| **Handshake Protocol**    | PSK exchange                            | **SAE** (Dragonfly) with **Forward Secrecy** |
| **Personal Mode**         | WPA2-Personal: PSK                      | WPA3-Personal: 128-bit SAE                   |
| **Enterprise Mode**       | WPA2-Enterprise: 802.1X/RADIUS          | WPA3-Enterprise: 192-bit SAE                 |
| **Data Integrity**        | CBC-MAC 64-bit MIC                      | SHA-2 for each input                         |
| **Protected Mgmt Frames** | Mandatory since 2018 (BIP-CMAC-AES-128) | Mandatory (BIP-CMAC-256)                     |
| **KRACK Vulnerable**      | ✅ Yes                                   | ❌ No (SAE exchange)                          |
| **Dictionary Attack**     | ✅ Yes                                   | ❌ Blocked after failed attempts              |

---

## 📋 DETAILED INSTRUCTOR NOTES

### Key Improvements — Technical Deep Dive

#### 1. Stronger Encryption: AES-GCM + Elliptic Curve

- **AES-GCM (Galois/Counter Mode)** provides both encryption AND authentication in one operation — more efficient than WPA2's AES-CCMP
- **Elliptic Curve Cryptography (ECC)** with CNSA Suite B — NSA-approved for protecting classified information
- 192-bit keys vs 128-bit — exponentially harder to brute-force

#### 2. Forward Secrecy — Game-Changer

WPA3's SAE provides **Perfect Forward Secrecy (PFS)**:

- Each session generates **unique, temporary keys**
- If an attacker records encrypted traffic today and later obtains the passphrase → **they still cannot decrypt past traffic**
- WPA2 PSK has NO forward secrecy — past traffic can be decrypted if passphrase is compromised

> 💡 **Real-world analogy:** Forward secrecy is like using a **different combination lock for every conversation** — even if someone discovers one combination, they can't open locks from previous conversations.

#### 3. Protected Management Frames (PMF)

- **WPA2:** PMF became mandatory in 2018 (previously optional)
- **WPA3:** PMF is mandatory from the start — cannot be disabled
- PMF protects management frames (deauthentication, disassociation) from being spoofed
- **Without PMF:** Attacker can send fake "deauthenticate" frames → kick clients off the network (DoS attack)
- **With PMF:** These frames are cryptographically signed → forgeries are rejected

#### 4. Enterprise Mode Upgrade (192-bit)

- WPA3-Enterprise uses **192-bit security suite** — aligns with government/defense requirements
- Suitable for highly sensitive environments (healthcare, finance, government)

---

---

# 📄 PAGE 135 — WPA3 SAE Handshake

---

## 🖥️ SLIDE SUMMARY

The slide shows the **complete WPA3 SAE handshake sequence**:

```
Client                              FortiAP
  |──── Probe Request ─────────────→|
  |←─── Probe Response ────────────|
  |──── Auth (SAE Commit) ─────────→| [Client scalar + element]
  |←─── Auth (SAE Commit) ─────────| [AP scalar + element]

[Both sides generate PMK independently using each other's values]

  |──── Auth (SAE Confirm) ────────→| [Client verification]
  |←─── Auth (SAE Confirm) ────────| [AP verification]
  |──── Association Request ───────→|
  |←─── Association Response ──────|
  |←──── 4-Way Handshake ─────────→|
  |       [Encrypted connection]    |
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### SAE Handshake — Step-by-Step Technical Breakdown

#### Step 1 & 2: Probe Request/Response

- Standard 802.11 probe exchange — client discovers the AP and its capabilities
- AP advertises WPA3/SAE support in its **RSN Information Element**

#### Steps 3 & 4: SAE Commit (The Magic of Dragonfly)

Both client and AP independently generate:

- A **random scalar** (a large random number)
- A **random element** (a point on an elliptic curve)

**Critical security property:** These random values are NOT related to the SAE password — they are freshly generated for each authentication attempt.

They exchange these values with each other. Then, **each side independently computes the PMK** using:

```
PMK = f(own_scalar, own_element, peer_scalar, peer_element, password)
```

Using **Elliptic Curve Diffie-Hellman (ECDH)** mathematics, both sides arrive at the **same PMK** without ever transmitting the password or the PMK itself.

> 💡 **Real-world analogy:** Imagine you and a friend agree on a PMK by each mixing your own secret color with a shared public color — you exchange the mixed colors publicly but an observer cannot determine your secret colors or the final mixture. This is the essence of Diffie-Hellman key exchange.

#### Steps 5 & 6: SAE Confirm (Proof of Knowledge)

- Each side sends a **confirmation value** — a hash proving they computed the same PMK
- If confirmations match → both sides have the same PMK → authentication successful
- If not → authentication fails (wrong password or attack attempt)

**Why this defeats dictionary attacks:**

- An attacker cannot test passwords offline — they need to complete a FULL SAE exchange for each guess
- The AP detects repeated failed attempts and **blocks further attempts** (rate limiting)
- SAE does not reveal any information that helps offline cracking

#### Steps 7 & 8: Association

- Standard 802.11 association — identical to WPA2

#### Step 9: Four-Way Handshake

- Same four-way handshake as WPA2 — derives PTK and GTK from the SAE-generated PMK
- Same encryption keys are produced — the difference is HOW the PMK was generated

---

### SAE vs. WPA2-PSK — The Key Difference

| Step                       | WPA2-PSK                               | WPA3-SAE                                   |
|----------------------------|----------------------------------------|--------------------------------------------|
| **PMK source**             | Derived directly from passphrase       | Derived from Dragonfly key exchange        |
| **Passphrase exposure**    | Hash derivable from captured handshake | Never exposed — exchange is zero-knowledge |
| **Offline cracking**       | ✅ Possible                             | ❌ Impossible                               |
| **Forward secrecy**        | ❌ No                                   | ✅ Yes                                      |
| **Brute force protection** | ❌ None                                 | ✅ Lockout after failed attempts            |

---

---

# 📊 Master Summary — Pages 126–135

```
┌─────────────────────────────────────────────────────────────────────┐
│          WIRELESS AUTHENTICATION & ENCRYPTION — FULL PICTURE        │
├─────────────────────────────────────────────────────────────────────┤
│  802.1X FRAMEWORK                                                   │
│  Supplicant (client) → Authenticator (FortiGate/AP) → Auth Server  │
│  Authenticator = transparent relay — makes NO auth decisions        │
├─────────────────────────────────────────────────────────────────────┤
│  EAP METHODS (Most → Least Secure)                                  │
│  EAP-TLS   → Certs on BOTH sides — hardest to deploy, most secure  │
│  PEAP      → Server cert only — most common, native OS support      │
│  EAP-TTLS  → Server cert only — supports legacy PAP/CHAP inside    │
│  LEAP      → Deprecated — do NOT use                               │
├─────────────────────────────────────────────────────────────────────┤
│  WPA2 KEY HIERARCHY                                                 │
│  PMK (from EAP or PSK)                                              │
│     └→ PTK (4-way handshake) → Encrypts UNICAST traffic           │
│  GMK └→ GTK (4-way handshake) → Encrypts BROADCAST/MULTICAST      │
├─────────────────────────────────────────────────────────────────────┤
│  WPA2 vs WPA3                                                       │
│  WPA2: PSK → PMK directly → vulnerable to offline dictionary attack │
│  WPA3: SAE → PMK via Dragonfly ECC → offline cracking impossible   │
│  WPA3 adds: Forward Secrecy, 192-bit keys, mandatory PMF           │
├─────────────────────────────────────────────────────────────────────┤
│  WPA3 SAE HANDSHAKE                                                 │
│  Commit (exchange scalars/elements) → PMK derived independently     │
│  Confirm (prove PMK match) → Associate → 4-Way Handshake           │
└─────────────────────────────────────────────────────────────────────┘
```

### Topic: Wireless Security — Security Modes, Authentication & Captive Portals

---

---

# 📄 PAGE 139 — Security Modes Overview

---

## 🖥️ SLIDE SUMMARY

The slide lists all **security modes** supported by the FortiGate wireless controller:

| Security Mode                          | Category                          |
|----------------------------------------|-----------------------------------|
| **WPA2 Personal** (PSK)                | Personal — shared passphrase      |
| **WPA2 Enterprise** (802.1X)           | Enterprise — RADIUS-based         |
| **WPA/WPA2 Personal + Captive Portal** | Mixed — PSK + web login           |
| **WPA3** (SAE)                         | Next-gen Personal                 |
| **Captive Portal**                     | Guest access                      |
| **Open**                               | No authentication                 |
| **OWE**                                | Opportunistic Wireless Encryption |
| **OSEN**                               | Online Signup Enabled Network     |

**Supported encryption formats:**

- **TKIP** (legacy — WPA only)
- **TKIP/AES** (mixed — transitional)
- **AES/CCMP** (WPA2 standard)
- **AES-GCM** (WPA3)

---

## 📋 DETAILED INSTRUCTOR NOTES

### Security Modes — The Big Picture

Security modes control **two things simultaneously**:

1. **How clients authenticate** — who is allowed to join?
2. **How traffic is encrypted** — how is data protected over the air?

Every SSID must have exactly **one security mode** assigned. Choosing the wrong mode for your use case is one of the most common wireless deployment mistakes.

---

### Two Authentication Philosophies

**Method 1 — Something you KNOW (credential-based):**

- WPA2 Personal → shared passphrase
- WPA2 Enterprise → username/password via RADIUS
- WPA3 SAE → passphrase via Dragonfly
- Captive Portal → username/password on web page

**Method 2 — No authentication (guest/open):**

- Open → completely unencrypted, no auth
- OWE → no auth but encrypted (upgrade to Open)
- Captive Portal (Disclaimer Only) → accept terms, no credentials needed

---

### OWE — Opportunistic Wireless Encryption

**OWE (Opportunistic Wireless Encryption)** is an important modern addition:

- Designed for **public/guest networks** where you don't want to require passwords
- Traffic is **encrypted** even though there's no authentication
- Protects against **passive eavesdropping** on open networks
- Replaces traditional "Open" networks in modern deployments

> 💡 **Real-world analogy:** OWE is like a coffee shop that gives every customer their own private table partition — anyone can sit down (no authentication), but no one can eavesdrop on your conversation (encryption).

---

### OSEN — Online Signup Enabled Network

- Used in **Passpoint/Hotspot 2.0** deployments
- Allows devices to **onboard and provision credentials** before full network access
- Common in **carrier Wi-Fi offload** and **venue Wi-Fi** (airports, stadiums)
- Client connects to OSEN network → completes online signup → receives proper credentials → reconnects to secure SSID

---

### TKIP vs. AES — Why TKIP is Legacy

| Cipher               | Standard            | Status            | Notes                                      |
|----------------------|---------------------|-------------------|--------------------------------------------|
| **TKIP** (RC4-based) | WPA (802.11i draft) | ❌ **Deprecated**  | Known vulnerabilities, slow, max 54 Mbps   |
| **AES/CCMP**         | WPA2 (802.11i)      | ✅ **Current**     | Strong, fast, required for 802.11n+ speeds |
| **AES-GCM**          | WPA3                | ✅ **Recommended** | Strongest, most efficient                  |

> ⚠️ **Trainer Warning:** TKIP limits your wireless throughput to **54 Mbps maximum** — it disables 802.11n/ac/ax high-speed features. If any client connects using TKIP, the entire BSS is dragged down to legacy speeds. Avoid TKIP in all new deployments.

---

---

# 📄 PAGE 140 — WPA2 Personal

---

## 🖥️ SLIDE SUMMARY

The slide illustrates **WPA2 Personal (PSK)** — where all users share the same passphrase:

```
User 1 ──┐
User 2 ──┤── SSID: Corp-WiFi ── FortiGate
User 3 ──┘    Shared Key: [same passphrase for everyone]
```

**Key bullet points:**

- All users and devices share the **same static passphrase**
- If a user leaves or a device is lost → key must be **changed on every AP and client**
- **Key length and complexity** is critically important for security

---

## 📋 DETAILED INSTRUCTOR NOTES

### How WPA2 Personal Works

The shared passphrase is used to derive the **PMK (Pairwise Master Key)**:

```
PMK = PBKDF2(passphrase, SSID, 4096 iterations, 256 bits)
```

- **PBKDF2** = Password-Based Key Derivation Function 2
- 4096 iterations of SHA-1 hash — makes brute-forcing computationally expensive
- The SSID name is included → same passphrase on different SSIDs = different PMK

---

### Security Weaknesses of WPA2 Personal

**Problem 1: No per-user identity**

- You know THAT someone connected — you don't know WHO
- All users look identical in logs
- Cannot apply per-user access policies

**Problem 2: Key distribution nightmare**

- 50 employees → passphrase must be communicated securely to all 50
- New employee joins → must be told the passphrase (securely)
- Employee leaves → passphrase must be changed for ALL remaining users

**Problem 3: Offline dictionary attacks**

- An attacker captures the four-way handshake (passive sniffing)
- Takes it offline and runs hashcat/aircrack-ng
- If passphrase is in a dictionary → cracked

---

### Passphrase Best Practices

| Criterion            | Minimum          | Recommended                                     |
|----------------------|------------------|-------------------------------------------------|
| **Length**           | 8 characters     | **20+ characters**                              |
| **Complexity**       | Mixed case       | Upper + Lower + Numbers + Symbols               |
| **Dictionary words** | Avoid            | Use random words or passphrase generator        |
| **Rotation**         | When compromised | Every **90 days** in high-security environments |

> 💡 **Trainer Tip:** Use a **passphrase generator** (e.g., diceware) to create long, memorable, non-dictionary passphrases. Example: `correct-horse-battery-staple-42!` — long, memorable, not in any dictionary.

---

### When Is WPA2 Personal Acceptable?

- **Home networks** with trusted family members
- **Small offices** with fewer than 5 trusted employees
- **IoT device networks** where 802.1X is not supported
- **Guest SSIDs** where basic access control is sufficient

> ⚠️ **Enterprise guidance:** For corporate networks with employee devices, **always use WPA2/WPA3 Enterprise** (802.1X/RADIUS). WPA2 Personal is not appropriate for environments requiring compliance (PCI-DSS, HIPAA, ISO 27001).

---

---

# 📄 PAGE 141 — MPSK Profile

---

## 🖥️ SLIDE SUMMARY

The slide introduces **MPSK (Multiple Pre-Shared Keys)** — a Fortinet enhancement to WPA2 Personal:

**MPSK key concepts:**

- **One MPSK profile** per SSID
- Profile contains **multiple MPSK groups**
- Each group has keys for a **single device or group of devices**
- **VLANs can be assigned** based on MPSK group (dynamic VLAN assignment)
- Enabling MPSK **discards the single pre-shared key**

**GUI Path:** `WiFi & Switch Controller > SSIDs`

**Management options:**

- Batch generate or import MPSK keys
- Export MPSK keys to **CSV file**
- Dynamically assign VLANs based on used MPSK
- Apply an **MPSK schedule**

---

## �� DETAILED INSTRUCTOR NOTES

### What Problem Does MPSK Solve?

Standard WPA2 Personal has one shared key — everyone uses it. MPSK gives **each device or group its own unique key**, while still using the WPA2 Personal framework.

```
Standard PSK:
  All 50 devices → same passphrase "Company@WiFi2024"

MPSK:
  IT Department (10 devices)    → Key: "IT-Group-Key-XK29"  → VLAN 10
  Finance Department (8 devices)→ Key: "Finance-Key-PQ77"   → VLAN 20
  IoT Sensors (30 devices)      → Key: "IoT-Sensor-Key-AB44"→ VLAN 30
  Guest Laptop (1 device)       → Key: "Guest-TempKey-123"  → VLAN 40
```

---

### MPSK Benefits Over Standard PSK

| Feature                      | Standard PSK             | MPSK                            |
|------------------------------|--------------------------|---------------------------------|
| **Keys per SSID**            | 1                        | Unlimited                       |
| **Per-device/group key**     | ❌                        | ✅                               |
| **Revoke one device**        | Must change for everyone | ❌ Delete that device's key only |
| **VLAN per group**           | ❌                        | ✅ Dynamic VLAN assignment       |
| **IoT segmentation**         | ❌                        | ✅ Each device type isolated     |
| **No 802.1X infrastructure** | ✅                        | ✅                               |

---

### Dynamic VLAN Assignment with MPSK — How It Works

When a device connects using its MPSK key:

1. FortiGate identifies **which MPSK group** the key belongs to
2. FortiGate automatically assigns the device to the **VLAN configured for that group**
3. Device receives IP from the **VLAN's DHCP scope**
4. Firewall policies for that VLAN apply automatically

This provides **network segmentation without 802.1X** — perfect for IoT devices that cannot support enterprise authentication.

> 💡 **Real-world use case:** A hospital uses one SSID with MPSK:
>
> - Medical devices (infusion pumps, monitors) → IoT VLAN, strict firewall rules
> - Staff laptops → Corporate VLAN, full access
> - Visitor tablets → Guest VLAN, internet only
> - All on the same SSID — but completely isolated from each other

---

### MPSK Schedule Feature

You can apply a **time-based schedule** to MPSK keys:

- A contractor's key works only **Monday-Friday, 9 AM–5 PM**
- A vendor's key expires after a specific date
- Guest keys automatically expire after the event

> ⚠️ **Trainer Warning:** When MPSK is enabled, the **single pre-shared key is deleted**. You cannot run both simultaneously. Plan your migration to MPSK carefully to avoid locking out existing devices.

---

---

# 📄 PAGE 142 — WPA3 SAE Configuration

---

## 🖥️ SLIDE SUMMARY

The slide shows the **FortiGate GUI configuration** for a WPA3 SAE SSID:

**Configuration options:**

- Enter **SAE password** (similar to WPA2 PSK entry)
- Optional: Enable **SAE-PK (SAE Public Key) authentication**
  - Requires generating a **private key** using third-party tools
  - Enter PK key + SAE password in SSID settings
  - Client side: only SAE password needed
- Optional: Enable **H2E (Hash-to-Element) only**
  - Provides secure cryptographic hash function for key exchange

**GUI Path:** `WiFi & Switch Controller > SSIDs`

---

## 📋 DETAILED INSTRUCTOR NOTES

### WPA3 SAE Configuration — Compared to WPA2 PSK

From a **GUI perspective**, WPA3 SAE looks almost identical to WPA2 Personal:

- You still enter a passphrase
- The difference is entirely in the **cryptographic protocol** used under the hood
- WPA3 uses **Dragonfly/SAE** instead of PSK derivation

---

### SAE-PK (SAE Public Key) — Advanced Feature

**SAE-PK** is an enhancement to standard WPA3 SAE:

**Problem it solves:** An evil twin AP can use the same SSID name and SAE password as a legitimate AP — clients can't tell which AP is real.

**How SAE-PK prevents this:**

- The legitimate AP has a **private key** (generated offline using third-party tools)
- The corresponding **public key is encoded into the password itself**
- When clients connect, SAE-PK verifies the AP possesses the private key matching the password
- A rogue AP with the same password but no private key → **authentication fails**

> �� **Real-world analogy:** SAE-PK is like a restaurant with a **secret ingredient** in their signature dish — anyone can see the recipe (password) but only the real restaurant can produce the authentic taste (private key proof).

**Important:** SAE-PK private key generation requires **third-party cryptographic tools** — FortiGate does not generate them internally.

---

### H2E (Hash-to-Element) — Cryptographic Improvement

**H2E** is an optimized method for deriving the SAE **Password Element (PE)**:

| Method                                 | Description                          | Security                          |
|----------------------------------------|--------------------------------------|-----------------------------------|
| **Hunting and Pecking** (original SAE) | Iterative trial-and-error to find PE | Vulnerable to timing side-channel |
| **H2E (Hash-to-Element)**              | Single deterministic hash operation  | ✅ No timing side-channel          |

When **H2E Only** is enabled:

- Only H2E is used for PE derivation — eliminates the hunting-and-pecking method entirely
- Faster computation + more secure
- ⚠️ Requires clients to support H2E — older WPA3 devices may not

---

---

# 📄 PAGE 143 — WPA3 SAE Transition Mode

---

## 🖥️ SLIDE SUMMARY

The slide explains **WPA3 SAE Transition** — a compatibility mode for mixed environments:

```
WPA3-capable device  → Connects using WPA3 SAE (full security)
WPA2-only device     → Connects using WPA2 Personal PSK (fallback)
                     ↓ Both connect to the SAME SSID
```

**GUI Path:** `WiFi & Switch Controller > SSIDs`

Two fields shown:

- **WPA3 SAE password** — for WPA3-capable devices
- **WPA2 PSK password** — for legacy devices that don't support WPA3

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Transition Mode Exists

WPA3 was released in 2018 but adoption is still in progress. In most real-world enterprise networks you will encounter:

- New laptops/phones → support WPA3
- Older devices (2017 and earlier) → WPA2 only
- Some IoT devices → WPA2 only (may never be updated)

**Without transition mode:** You must choose — WPA3 only (locks out old devices) or WPA2 only (no WPA3 benefits)

**With transition mode:** Same SSID serves both — modern devices get WPA3 security, legacy devices fall back to WPA2

---

### How Transition Mode Works Technically

The AP broadcasts **both RSN (WPA3) and WPA2 capabilities** in its beacon frames:

```
Beacon Information Elements:
  RSN IE: WPA3-SAE supported
  RSN IE: WPA2-PSK supported (backward compat)
```

Client selection process:

- WPA3-capable client sees WPA3 → connects via SAE
- WPA2-only client sees WPA2 → connects via PSK
- Both get network access — different security level

---

### Security Consideration in Transition Mode

> ⚠️ **Important:** Transition mode is a security compromise. WPA2 PSK connections in transition mode are still vulnerable to **offline dictionary attacks**. Transition mode should be a **temporary state** while you upgrade legacy devices — not a permanent configuration.

**Migration path:**

```
Phase 1: WPA3 SAE Transition (WPA3 + WPA2 fallback)
   ↓ Upgrade/replace legacy devices
Phase 2: WPA3 SAE Only (maximum security)
```

---

---

# 📄 PAGE 144 — WPA2 and WPA3 Enterprise

---

## 🖥️ SLIDE SUMMARY

The slide covers **enterprise (RADIUS-based) security modes**:

| Mode                     | Encryption          | PMF             | RADIUS     |
|--------------------------|---------------------|-----------------|------------|
| **WPA2 Enterprise**      | 128-bit AES         | Optional        | ✅ Required |
| **WPA3 Enterprise Only** | 128-bit AES         | ✅ **Mandatory** | ✅ Required |
| **WPA3 Enterprise**      | **192-bit AES-GCM** | ✅ **Mandatory** | ✅ Required |

**CLI configuration for WPA3 Enterprise:**

```bash
config wireless-controller vap
    edit fortinet
        set ssid fortinet
        set security wpa3-enterprise
        set pmf enable
        set auth radius
        set radius-server "wifi-radius"
        set schedule "always"
    end
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### Three Enterprise Modes — When to Use Each

#### WPA2 Enterprise

- The **current standard** for most enterprise deployments
- Uses 802.1X/EAP with RADIUS backend
- 128-bit AES/CCMP encryption
- PMF optional (but should be enabled)
- **Compatible with all modern devices**
- Best for: Most enterprise environments

#### WPA3 Enterprise Only

- **WPA3 SAE** for authentication + 128-bit AES
- PMF is **automatically enabled** — cannot be disabled
- Same encryption strength as WPA2 Enterprise but with SAE protection
- Best for: Environments moving to WPA3 but not requiring 192-bit suite

#### WPA3 Enterprise (192-bit Suite)

- Uses **CNSA (Commercial National Security Algorithm) Suite B**
- 192-bit AES-GCM encryption — NSA-approved for classified information
- PMF **mandatory**
- Requires clients with 192-bit suite support
- Best for: **Government, defense, healthcare, finance** — highly regulated industries

---

### PMF Enforcement in WPA3 Enterprise

When WPA3 Enterprise is configured via CLI:

```bash
set pmf enable
```

PMF enables **cryptographic protection of management frames**:

| Attack                    | Without PMF                           | With PMF                      |
|---------------------------|---------------------------------------|-------------------------------|
| **Deauth flood**          | ✅ Attacker can disconnect all clients | ❌ Fake deauth frames rejected |
| **Disassociation attack** | ✅ Easy DoS against clients            | ❌ Blocked                     |
| **Honeypot AP**           | ✅ Clients may associate to rogue AP   | ❌ Much harder                 |

> ⚠️ **Trainer Warning:** In WPA3 Enterprise, PMF is **automatically enabled** and **cannot be disabled**. Ensure all client devices support PMF before migrating — some very old drivers don't support it.

---

### Local vs. Remote RADIUS Server

FortiGate supports two RADIUS deployment options:

- **Local RADIUS** — FortiAuthenticator on the same network segment
- **Remote RADIUS** — RADIUS server in datacenter/cloud, accessed via WAN

The CLI command `set radius-server "wifi-radius"` references a **pre-configured RADIUS server object** in FortiGate (configured under `User & Authentication > RADIUS Servers`).

---

---

# �� PAGE 145 — 802.1X Local Authentication

---

## 🖥️ SLIDE SUMMARY

The slide explains **Local EAP authentication** — where FortiGate acts as both:

```
Client → [FortiGate = Authenticator + Authentication Server] → Network Access
```

**Key characteristics:**

- FortiGate plays **dual role**: Authenticator AND Authentication Server
- Only valid for **PEAP** (not EAP-TLS or EAP-TTLS)
- Uses **local user database** on FortiGate
- Users must be in **local user groups** applied to the SSID
- Default: uses **factory certificates** (can be replaced with custom certs)

**GUI Paths:**

- SSID config: `WiFi & Switch Controller > SSIDs`
- Certificate management: `System > WiFi Settings`

---

## �� DETAILED INSTRUCTOR NOTES

### When to Use Local Authentication

Local 802.1X is ideal for:

- **Small deployments** without a dedicated RADIUS server
- **Lab/test environments** where simplicity is preferred
- **Branch offices** that need 802.1X but have no local RADIUS infrastructure

> ⚠️ **Limitation:** Local authentication only supports **PEAP** — not EAP-TLS or EAP-TTLS. If you need certificate-based auth (EAP-TLS), you need an external RADIUS server like FortiAuthenticator.

---

### Configuration Workflow

**Step 1:** Create local users `User & Authentication > User Definition` → Create username/password accounts

**Step 2:** Create a user group `User & Authentication > User Groups` → Add users to a group (e.g., "WiFi-Users")

**Step 3:** Apply to SSID `WiFi & Switch Controller > SSIDs` → Select **Local** authentication → Select user group

**Step 4:** Configure certificate (optional) `System > WiFi Settings` → Upload custom server certificate (replace factory default)

---

### Factory Certificate Warning

By default, FortiGate uses its **built-in self-signed factory certificate** for PEAP:

- Client will see a **certificate warning** — "Certificate not trusted"
- Users must **manually accept** the warning
- This is a **security risk** — users trained to accept cert warnings become vulnerable to MITM attacks

**Best practice:** Upload a certificate signed by your **internal CA** or a **trusted public CA**:

- No certificate warnings for users
- Clients automatically trust the certificate
- Prevents social engineering attacks exploiting cert acceptance habits

> 💡 **Trainer Tip:** FortiAuthenticator can act as your internal CA and issue server certificates for FortiGate — fully integrated with the Security Fabric.

---

---

# 📄 PAGE 146 — Remote RADIUS Authentication

---

## 🖥️ SLIDE SUMMARY

The slide covers **remote RADIUS authentication** — the standard enterprise 802.1X deployment:

```
Client → FortiGate (Authenticator only) → Remote RADIUS Server → Access-Accept/Reject
```

**Key points:**

- FortiGate is the **authenticator only** — NOT the auth server
- Authentication forwarded to a **pre-configured remote RADIUS server**
- Must configure RADIUS server first: `User & Authentication > RADIUS Servers`
- Apply RADIUS server to SSID: `WiFi & Switch Controller > SSIDs`

---

## �� DETAILED INSTRUCTOR NOTES

### Remote RADIUS — The Enterprise Standard

In enterprise environments, the RADIUS server is typically:

- **FortiAuthenticator** — Fortinet's dedicated AAA server
- **Microsoft NPS (Network Policy Server)** — integrated with Active Directory
- **FreeRADIUS** — open-source Linux-based RADIUS
- **Cisco ISE** — enterprise NAC + RADIUS platform
- **Aruba ClearPass** — enterprise NAC platform

---

### RADIUS Configuration on FortiGate

**Step 1:** Configure the RADIUS server object `User & Authentication > RADIUS Servers` → New:

```
Name: wifi-radius
Server IP/Name: 192.168.1.100
Secret: [shared secret — must match RADIUS server config]
Authentication method: Auto (or specify PEAP/EAP-TLS)
```

**Step 2:** Apply to SSID `WiFi & Switch Controller > SSIDs` → Security Mode: WPA2/WPA3 Enterprise → Select RADIUS server object

---

### RADIUS Shared Secret — Critical Security Element

The **shared secret** is a password shared between:

- FortiGate (RADIUS client)
- The RADIUS server

It is used to **encrypt RADIUS messages** between them. If compromised:

- An attacker could impersonate FortiGate to the RADIUS server
- Authentication can be bypassed

**Best practices:**

- Use a **long, random shared secret** (32+ characters)
- **Never reuse** the same secret across multiple RADIUS clients
- Store securely — treat it like a private key

---

### RADIUS Communication Flow

```
1. Client connects to SSID → sends EAP Identity
2. FortiGate → RADIUS: Access-Request (with EAP payload)
3. RADIUS → FortiGate: Access-Challenge (EAP challenge)
4. FortiGate → Client: EAP Request
5. Client → FortiGate: EAP Response (credentials)
6. FortiGate → RADIUS: Access-Request (with credentials)
7. RADIUS validates against AD/local DB
8. RADIUS → FortiGate: Access-Accept (or Reject)
9. FortiGate → Client: EAP Success → 4-Way Handshake
10. Client on network
```

---

---

# 📄 PAGE 147 — Wireless Single Sign-On (WSSO)

---

## 🖥️ SLIDE SUMMARY

The slide introduces **WSSO (Wireless Single Sign-On)**:

**What WSSO does:**

- Maps **remote RADIUS users** to **local FortiGate groups**
- Enables **different levels of network access** per group using firewall policies
- Enforces **security profiles** based on user group membership

**Technical requirements:**

- RADIUS server must send **Vendor Specific Attribute (VSA):**
  - Attribute name: `FORTINET-GROUP-NAME`
  - Fortinet Vendor ID: **12356**
- Local user groups on FortiGate must have the **exact same name** as the RADIUS attribute
- Group name is **case sensitive**

---

## �� DETAILED INSTRUCTOR NOTES

### Why WSSO Matters — The Business Problem

Without WSSO, all wireless users on the same SSID get the **same firewall policy**:

```
All WiFi users → Same policy → Same network access
```

With WSSO, FortiGate knows each user's **group membership**:

```
WiFi user "john.doe" → Group: "Finance" → Finance firewall policy → Finance VLAN, limited access
WiFi user "jane.smith" → Group: "IT-Admin" → IT policy → Full network access
WiFi user "guest@company" → Group: "Guest" → Guest policy → Internet only
```

All on the **same SSID** — but completely different access levels based on identity.

---

### WSSO vs. Standard 802.1X

| Feature                    | Standard 802.1X     | WSSO                               |
|----------------------------|---------------------|------------------------------------|
| **Authentication**         | ✅ Yes               | ✅ Yes                              |
| **Group-based policy**     | ❌ Not automatically | ✅ Yes — dynamic                    |
| **RADIUS attributes used** | Standard only       | **Fortinet VSA** (vendor-specific) |
| **Dynamic access control** | Limited             | ✅ Full                             |

---

### RADIUS VSA Configuration — Technical Detail

On the RADIUS server (e.g., FortiAuthenticator or NPS), you must configure it to send back:

```
RADIUS Access-Accept reply attributes:
  Vendor-Specific (Vendor: Fortinet, ID: 12356)
    Attribute: FORTINET-GROUP-NAME
    Value: "Finance"    ← must exactly match local group name on FortiGate
```

**On FortiGate NPS (Windows Active Directory example):**

- Add Fortinet dictionary to NPS
- Create Network Policy → Conditions: AD group membership
- Settings: RADIUS Attributes → Add Vendor-Specific → Fortinet-Group-Name

---

### Case Sensitivity Warning

The slide explicitly states: **"Group name value is case sensitive"**

This is a very common deployment mistake:

```
RADIUS sends:  "Finance"     ← Capital F
FortiGate group: "finance"   ← Lowercase f
Result: WSSO FAILS — no match found
```

Always **copy-paste** the group name between RADIUS config and FortiGate — never type it manually twice.

---

---

# 📄 PAGE 148 — WSSO Configuration

---

## 🖥️ SLIDE SUMMARY

The slide shows the **three-component WSSO configuration workflow**:

```
Step 1: Create local user groups on FortiGate
        Authentication > User Management
        (Groups contain ONLY the name matching RADIUS attribute)

Step 2: Configure RADIUS server on FortiGate
        User & Authentication > User Group

Step 3: Apply RADIUS server to SSID
        WiFi & Switch Controller > SSIDs
```

**Key note:** Local user groups on FortiGate contain **only the group name** — no actual users are added to these groups locally (users exist in RADIUS/AD).

---

## 📋 DETAILED INSTRUCTOR NOTES

### WSSO Group Structure — Common Misconception

A common misunderstanding: students think they need to add users to the local FortiGate groups.

**In WSSO, the local group on FortiGate is just a NAME CONTAINER:**

```
FortiGate Local Group: "Finance"
  Members: [EMPTY — no local users]
  ← FortiGate uses this name to match what RADIUS sends back
```

The **actual users exist in the RADIUS server / Active Directory**. When RADIUS authenticates a user and sends back `FORTINET-GROUP-NAME = "Finance"`, FortiGate finds its local group named "Finance" and applies that group's firewall policies to the user.

---

### Complete WSSO Configuration Steps

**Step 1: Create Local Groups on FortiGate**

```
User & Authentication > User Groups > Create New
  Name: Finance        ← Must exactly match RADIUS VSA value
  Type: Firewall
  Members: [leave empty]
```

**Step 2: Configure RADIUS Server**

```
User & Authentication > RADIUS Servers > Create New
  Name: wifi-radius
  IP: [RADIUS server IP]
  Secret: [shared secret]
  Enable NAS IP: [FortiGate IP]
```

**Step 3: Configure SSID**

```
WiFi & Switch Controller > SSIDs
  Security: WPA2/WPA3 Enterprise
  RADIUS Server: wifi-radius
```

**Step 4: Configure Firewall Policies**

```
Policy & Objects > Firewall Policy
  Source: [SSID interface]
  Source User/Group: Finance ← Apply group-specific policies
  Destination: [appropriate destination]
  Security Profiles: [Finance-appropriate profiles]
```

---

---

# 📄 PAGE 149 — Captive Portal Types

---

## 🖥️ SLIDE SUMMARY

The slide introduces **three captive portal types** and their supported traffic modes:

| Captive Portal Type             | What User Must Do                  | Supported Traffic Mode |
|---------------------------------|------------------------------------|------------------------|
| **Authentication**              | Provide login credentials          | Tunnel + Bridge        |
| **Disclaimer + Authentication** | Accept disclaimer AND authenticate | Tunnel only            |
| **Disclaimer Only**             | Accept disclaimer (no credentials) | Tunnel only            |

---

## 📋 DETAILED INSTRUCTOR NOTES

### What Is a Captive Portal?

A **captive portal** is a web page that intercepts wireless clients before granting network access:

1. Client connects to the SSID (no credentials required at Wi-Fi level)
2. Client gets an IP address via DHCP
3. Client tries to open any website
4. FortiGate **redirects** all HTTP traffic to the captive portal page
5. Client completes required action (login, disclaimer acceptance)
6. FortiGate **opens the controlled port** → full network access granted

> 💡 **Real-world analogy:** Captive portals are exactly like hotel Wi-Fi or airport Wi-Fi — you connect, but before you can browse, you're redirected to a login/agreement page.

---

### Three Portal Types — When to Use Each

#### 1. Authentication Portal

- Best for: **Named guest accounts**, **contractor access**, **hotspot billing**
- Users must know their username/password
- Credentials can be from:
  - **Local FortiGate database**
  - **Remote RADIUS server**
  - **FortiAuthenticator** (with self-registration, social login, SMS OTP)

#### 2. Disclaimer + Authentication

- Best for: **Corporate guest policy compliance**
- Users must:
  - Read and accept **legal terms of use**
  - Then authenticate with credentials
- Creates audit trail: this person accepted these terms at this time
- **Tunnel mode only** — all traffic inspected by FortiGate

#### 3. Disclaimer Only

- Best for: **Public/open guest access** (conference rooms, lobbies, public areas)
- No credentials needed — just click "I Accept"
- Lowest friction for guests — anyone can connect
- **Tunnel mode only** — FortiGate still inspects all traffic
- ⚠️ No user identity — cannot track who used the network

---

### Traffic Mode Restrictions

The table shows an important limitation:

- **Authentication** portal → works with Tunnel AND Bridge mode
- **Disclaimer + Auth** → Tunnel mode ONLY
- **Disclaimer Only** → Tunnel mode ONLY

> ⚠️ **Trainer Warning:** If you plan to use Disclaimer or Disclaimer+Auth captive portal, you must use Tunnel Mode SSID. Plan your SSID type accordingly during design — remember, traffic mode cannot be changed after SSID creation.

---

---

# 📄 PAGE 150 — Captive Portal Types (Continued)

---

## 🖥️ SLIDE SUMMARY

The slide covers the **two captive portal authentication portal types**:

| Portal Type  | Who Hosts It    | How It Works                                                             |
|--------------|-----------------|--------------------------------------------------------------------------|
| **Local**    | FortiGate       | FortiGate presents login page + processes authentication                 |
| **External** | External server | FortiGate redirects to external URL → external server handles everything |

**GUI configuration:**

- Enable captive portal on SSID: `WiFi & Switch Controller > SSIDs`
- Authenticated users must be part of the **specified user group**

---

## �� DETAILED INSTRUCTOR NOTES

### Local Captive Portal

**How it works:**

1. Client connects to SSID and gets IP from DHCP
2. Client opens browser → any URL
3. FortiGate intercepts → redirects to `https://[FortiGate-IP]/fgtauth`
4. FortiGate presents its **built-in login page**
5. Client enters credentials → FortiGate validates locally or via RADIUS
6. If valid → FortiGate opens access

**Customization options:**

- Custom logo, background image, welcome message
- Multi-language support
- Terms of service text
- Redirect URL after successful login (send user to company homepage)

**GUI path for customization:** `System > Replacement Messages > WiFi` → Edit the captive portal HTML template

---

### External Captive Portal

**How it works:**

1. Client connects to SSID and gets IP
2. Client opens browser → any URL
3. FortiGate intercepts → redirects to **external URL** (e.g., [`https://portal.company.com`](https://portal.company.com))
4. External server presents login page
5. External server authenticates user
6. External server **notifies FortiGate** (via RADIUS or API) that user is authenticated
7. FortiGate grants network access

**When to use External Captive Portal:**

- **Branded guest portals** — customized beyond FortiGate's built-in capabilities
- **Social login** — "Login with Google/Facebook/LinkedIn"
- **SMS OTP verification** — send one-time code to mobile number
- **Self-registration workflow** — guest fills form, gets emailed credentials
- **Billing/payment integration** — hotel/hotspot pay-per-use model
- **FortiAuthenticator as external portal** — adds all above capabilities

---

### User Group Requirement

The slide notes: **"Authenticated users must be part of the specified group"**

This means:

- You define a **user group** on FortiGate (e.g., "CaptivePortal-Users")
- Only users who belong to this group are granted access after authentication
- Firewall policies reference this group for access control

**Why this matters:**

- Even if someone enters valid credentials, if they're NOT in the allowed group → access denied
- Provides an additional layer of access control beyond just authentication

---

---

# 📊 Master Summary — Pages 139–150

```
┌─────────────────────────────────────────────────────────────────────────┐
│              WIRELESS SECURITY MODES — COMPLETE GUIDE                   │
├──────────────────┬──────────────────────────────────────────────────────┤
│ SECURITY MODE    │ KEY CHARACTERISTICS                                  │
├──────────────────┼──────────────────────────────────────────────────────┤
│ WPA2 Personal    │ One shared key — easy but limited scalability        │
│ MPSK             │ Multiple keys per SSID — per device/group + VLAN    │
│ WPA3 SAE         │ Dragonfly handshake — no offline cracking possible   │
│ WPA3 Transition  │ WPA3 for new devices + WPA2 fallback for legacy      │
│ WPA2 Enterprise  │ 802.1X/RADIUS — per-user identity, 128-bit AES      │
│ WPA3 Enterprise  │ 802.1X/RADIUS — 192-bit, mandatory PMF              │
├──────────────────┼──────────────────────────────────────────────────────┤
│ 802.1X AUTH      │ KEY FACTS                                            │
├──────────────────┼──────────────────────────────────────────────────────┤
│ Local Auth       │ FortiGate = Authenticator + Server, PEAP only       │
│ Remote RADIUS    │ FortiGate = Authenticator only, any EAP method      │
│ WSSO             │ RADIUS VSA maps users to local groups for policies   │
│                  │ VSA: FORTINET-GROUP-NAME (Vendor ID: 12356)          │
│                  │ ⚠️ Case sensitive — must match exactly              │
├──────────────────┼──────────────────────────────────────────────────────┤
│ CAPTIVE PORTAL   │ TYPES & MODES                                        │
├──────────────────┼──────────────────────────────────────────────────────┤
│ Authentication   │ Username/password → Tunnel + Bridge                 │
│ Disclaimer+Auth  │ Accept terms + login → Tunnel ONLY                  │
│ Disclaimer Only  │ Accept terms only → Tunnel ONLY                     │
│ Local portal     │ FortiGate hosts page + processes auth               │
│ External portal  │ FortiGate redirects to external URL                 │
└──────────────────┴──────────────────────────────────────────────────────┘

⚠️ KEY RULES TO REMEMBER:
  1. MPSK disables the single PSK when enabled
  2. WPA3 Enterprise always enables PMF automatically
  3. WSSO group names are CASE SENSITIVE
  4. Disclaimer portals require TUNNEL MODE SSID
  5. Local 802.1X auth supports PEAP ONLY
```

---
### Topic: Wireless Network Access — SSID Control & Client Isolation
# SSID Options
The slide presents **three key SSID control options** available in the FortiGate GUI:

| Option                                 | Default      | Purpose                                                   |
|----------------------------------------|--------------|-----------------------------------------------------------|
| **Maximum clients per SSID**           | Unlimited    | Limit how many clients can connect simultaneously         |
| **Disable SSID broadcast**             | Broadcast ON | Hide SSID from public view                                |
| **Vendor-specific element in beacons** | Disabled     | Embed FortiAP info (name, model, serial) in beacon frames |

**GUI Path:** `WiFi & Switch Controller > SSIDs`

The slide also shows a **packet capture of beacon frames** — demonstrating the vendor-specific element visible in real wireless traffic analysis tools.

### Option 1 — Maximum Number of Clients

**What it does:** Limits the total number of wireless clients that can associate to a specific SSID at any given time.

**Why it matters:** Each connected client consumes:

- **Radio airtime** — shared medium, more clients = less per-client bandwidth
- **FortiGate processing** — in Tunnel Mode, every client's traffic flows through FortiGate
- **DHCP leases** — each client takes an IP from the DHCP pool
- **AP memory** — each association entry stored in AP RAM

**Real-world scenario:** Imagine a conference room AP with an SSID intended for 30 attendees:

```
Without max clients: 300 people try to connect → AP overwhelmed
                     → Everyone gets terrible performance

With max clients = 30: First 30 connect fine
                        → Client 31 gets "cannot connect" → must wait
                        → First 30 enjoy consistent performance
```

> 💡 **Real-world analogy:** Maximum clients is like a **fire safety capacity limit** for a room — once it's full, no more people enter until someone leaves.

**Best practice guidance:**

- Set based on **expected concurrent users** — not maximum possible users
- A general rule: **25–30 clients per radio** for good performance
- For high-density environments (auditoriums, stadiums): **lower per-AP limits** with more APs
- For IoT networks: set a tight limit to prevent unauthorized device sprawl

**Configuration:**

```
WiFi & Switch Controller > SSIDs > [Select SSID]
  Max Clients: [enter number, e.g., 30]
  → 0 = unlimited (default)
```
### Option 2 — Disable SSID Broadcast (Hidden SSID)

**What it does:** When enabled, the AP **stops including the SSID name** in its beacon frames. The SSID becomes "hidden" — it won't appear in the Wi-Fi network list on client devices.

**How it works technically:** Normal beacon frame:

```
Beacon Frame:
  SSID: "Corp-WiFi"    ← SSID name visible to everyone
  Capabilities: ...
  RSN IE: WPA2/WPA3
```

Hidden SSID beacon frame:

```
Beacon Frame:
  SSID: "" (empty/null)  ← SSID name omitted
  Capabilities: ...
  RSN IE: WPA2/WPA3
```

Clients who **know the SSID name** can still manually enter it and connect — the AP still responds to directed probe requests.

**Two stated benefits from the slide:**

**Benefit 1 — Increased security:**

- Casual attackers scanning for networks won't see the SSID in their list
- Reduces the chance of unauthorized connection attempts
- Adds a layer of **"security through obscurity"**

**Benefit 2 — Reduced management traffic:**

- Fewer probe requests from devices looking for unknown networks
- Less airtime consumed by discovery traffic
- Slightly cleaner RF environment

**The hidden SSID security debate — instructor perspective:**

> ⚠️ **Trainer Warning:** Hidden SSIDs provide **very limited real security**. Any wireless scanning tool (Wireshark, Kismet, NetSpot) can detect hidden SSIDs through:
>
> - **Passive monitoring** of probe responses when a legitimate client connects
> - **Active probing** — tools send directed probe requests using common SSID names
> - **Analyzing beacon frames** — the AP still broadcasts, just without the SSID name field

Hidden SSID is best described as **deterrence against casual users**, not a security control against determined attackers. Never rely on SSID hiding as your primary security measure — always combine with strong encryption (WPA3) and authentication (802.1X).

> 💡 **Real-world analogy:** Hiding your SSID is like removing the sign from your front door — your neighbors who know where you live can still knock. Someone determined to find you will check every door on the street anyway.

### Option 3 — Vendor-Specific Element in Beacon Frames

**What it does:** When enabled, FortiAP includes a **Fortinet-proprietary information element (IE)** in every beacon frame, containing:

- **FortiAP name** (as configured on FortiGate)
- **FortiAP model number** (e.g., FAP-431F)
- **FortiAP serial number**

**What is a beacon frame?** Beacon frames are sent by APs approximately **10 times per second** to announce their presence and capabilities. Every wireless device in range receives these frames — they're how your phone "sees" available Wi-Fi networks.

**Normal beacon frame contains:**

- SSID name
- Supported data rates
- Security capabilities (WPA2/WPA3)
- Channel information

**With vendor-specific element enabled:**

- All of the above, PLUS
- FortiAP name, model, serial number (in a Fortinet-proprietary IE)

**Two use cases from the instructor notes:**

**Use Case 1 — Active site survey during walk-testing:**

```
Wireless admin walks through building with laptop running Wireshark/packet capture
↓
Continuously monitors beacon frames from target FortiAP
↓
Vendor IE shows exact AP name/serial → confirms which AP's signal is being measured
↓
Admin moves further away → signal strength drops → notes coverage boundary
↓
Result: Accurate RF coverage map with confirmed AP identification
```

Without this feature, an admin doing a walk test might capture beacons from **neighboring APs** and accidentally map the wrong AP's coverage area.

**Use Case 2 — Post-implementation verification:**

```
After deployment, admin performs validation survey
↓
Scans the environment → sees FortiAP beacon with known serial number
↓
Confirms this specific physical AP is broadcasting correctly
↓
Can immediately identify if an AP has been moved, replaced, or is transmitting from wrong location
```

> 💡 **Real-world analogy:** The vendor-specific element is like **a name tag on every radio transmission** — instead of just hearing a signal, you know exactly which specific AP it came from, by name and serial number.

> ⚠️ **Security consideration:** Advertising model and serial number in beacon frames also gives potential attackers information about your hardware. In high-security environments, disable this feature when not performing active surveys.

# SSID Groups

The slide introduces **SSID Groups** — a management simplification feature:

**Key concepts:**

- SSID Groups are **optional** — not required but useful for large deployments
- SSIDs are **members** of SSID groups
- An SSID group can be specified in a **FortiAP profile** the same way a single SSID is specified
- Creating an SSID group assigns **multiple SSIDs at once** to APs using that profile

**GUI Path:** `WiFi & Switch Controller > SSIDs > Create New > SSID Group`

### What Problem Do SSID Groups Solve?

In a deployment without SSID groups, managing multiple SSIDs across many APs looks like this:

**Without SSID Groups (manual, repetitive):**

```
FortiAP Profile "Campus-AP" → SSID: Corp-WiFi
FortiAP Profile "Campus-AP" → SSID: Guest-WiFi    ← must add each one individually
FortiAP Profile "Campus-AP" → SSID: IoT-WiFi
FortiAP Profile "Campus-AP" → SSID: Voice-WiFi

FortiAP Profile "Branch-AP" → SSID: Corp-WiFi
FortiAP Profile "Branch-AP" → SSID: Guest-WiFi    ← repeat for every profile
FortiAP Profile "Branch-AP" → SSID: IoT-WiFi
```

**With SSID Groups:**

```
SSID Group "Standard-SSIDs":
  Members: Corp-WiFi, Guest-WiFi, IoT-WiFi, Voice-WiFi

FortiAP Profile "Campus-AP" → SSID Group: Standard-SSIDs  ← one selection = 4 SSIDs
FortiAP Profile "Branch-AP" → SSID Group: Standard-SSIDs  ← same group, one selection
```

Adding a new SSID to the group → **automatically applies to ALL AP profiles** referencing that group. No need to edit each profile individually.

### SSID Groups in Practice — Configuration Steps

**Step 1: Create individual SSIDs**

```
WiFi & Switch Controller > SSIDs > Create New > SSID
  - Create each SSID (Corp-WiFi, Guest-WiFi, IoT-WiFi, etc.)
  - Configure security, DHCP, traffic mode per SSID
```

**Step 2: Create an SSID Group**

```
WiFi & Switch Controller > SSIDs > Create New > SSID Group
  Name: Standard-SSIDs
  Member SSIDs: [select Corp-WiFi, Guest-WiFi, IoT-WiFi]
```

**Step 3: Apply SSID Group to FortiAP Profile**

```
WiFi & Switch Controller > FortiAP Profiles > [Select Profile]
  SSID: [select SSID Group "Standard-SSIDs"]
  ← All member SSIDs are automatically broadcast by APs using this profile
```

### Scalability Benefit — Real Enterprise Example

Consider a retail chain with **200 stores**, each running the same 4 SSIDs:

```
Without groups: 200 profiles × 4 SSID assignments = 800 individual configurations
                Adding a new SSID = 200 profile edits

With groups:    200 profiles × 1 group assignment = 200 configurations
                Adding a new SSID = 1 group edit → automatically updates all 200 profiles
```

> 💡 **Real-world analogy:** SSID Groups are like a **TV channel package** — instead of subscribing to 50 channels individually, you subscribe to the "Premium Package" and get all 50 channels in one selection. Adding a new channel to the package automatically gives it to all package subscribers.

### SSID Groups and Radio Limits

> ⚠️ **Important operational limit:** Most FortiAP models support a maximum of **8 SSIDs per radio**. When using SSID groups, ensure the total number of SSIDs in the group does not exceed this limit.

Each SSID consumes airtime overhead for beacon broadcasts (remember — beacons are sent \~10 times/second per SSID). Too many SSIDs:

- Wastes airtime on management traffic
- Reduces available airtime for actual client data
- **Best practice: 3–5 SSIDs per radio maximum** in most deployments

# Block Intra-SSID Traffic

The slide explains the **Block Intra-SSID Traffic** feature and its **different behavior based on SSID mode**:

| SSID Mode       | Block Intra-SSID Behavior                                                                                       |
|-----------------|-----------------------------------------------------------------------------------------------------------------|
| **Tunnel Mode** | ALL traffic between wireless clients on the same SSID is blocked — across all APs                               |
| **Bridge Mode** | Only traffic between clients on the **same AP** is blocked — clients on **different APs** can still communicate |

**Additional note for Bridge Mode:** To block cross-AP client communication in Bridge Mode → must enable **Private VLAN** (if APs are connected to FortiSwitch)

**GUI Path:** `WiFi & Switch Controller > SSIDs`

### What Is Intra-SSID Traffic?

**Intra-SSID traffic** = direct communication **between wireless clients** connected to the same SSID:

```
Laptop A ────────────────────────────→ Laptop B
(Connected to Corp-WiFi)                (Connected to Corp-WiFi)
          ↑ This is intra-SSID traffic ↑
```

Without blocking, wireless clients on the same SSID can communicate directly — like being on the same wired switch segment.

### Why Block Intra-SSID Traffic?

**Security use cases:**

**1. Guest networks:**

- Guests should access the internet — NOT each other's devices
- Without blocking: Guest A could scan and attack Guest B's laptop
- With blocking: Complete isolation — each guest sees only the internet

**2. IoT networks:**

- IoT sensors should communicate only with their cloud servers — NOT with each other
- Prevents a compromised IoT device from attacking other devices on the same SSID
- Limits lateral movement in case of device compromise

**3. BYOD (Bring Your Own Device) networks:**

- Employee personal devices should not be able to reach each other
- Reduces risk of data leakage between personal devices

> 💡 **Real-world analogy:** Block intra-SSID is like **individual hotel rooms** — guests can all access the hotel lobby (internet) but cannot enter each other's rooms (client-to-client communication blocked).

### Tunnel Mode Behavior — Full Isolation

In **Tunnel Mode**, the Block Intra-SSID option provides **complete isolation**:

```
Client A (AP1) ──→ CAPWAP Tunnel ──→ FortiGate
                                          ↕  BLOCKED here
Client B (AP2) ──→ CAPWAP Tunnel ──→ FortiGate
```

**Why it's fully effective in Tunnel Mode:**

- ALL client traffic flows to FortiGate through the CAPWAP tunnel
- FortiGate sits in the middle of ALL client-to-client communication paths
- FortiGate simply drops any packet where:
  - Source = wireless client
  - Destination = another wireless client on the same SSID
- This works **regardless of which AP** the clients are connected to
- Even clients on **completely different APs** at different ends of the building are blocked from each other

### Bridge Mode Behavior — Per-AP Isolation Only

In **Bridge Mode**, the limitation becomes significant:

```
Scenario 1: Both clients on the SAME AP
Client A ──┐
            ├── FortiAP-1 ──→ [BLOCKED by AP directly]
Client B ──┘

Scenario 2: Clients on DIFFERENT APs
Client A ── FortiAP-1 ──→ Switch ──→ FortiAP-2 ── Client B
                     ↑ Communication NOT blocked ↑
```

**Why Bridge Mode cannot provide full isolation:**

- In Bridge Mode, client traffic exits locally at the AP — it NEVER reaches FortiGate
- The AP can block clients connected to **itself** from talking to each other
- But once traffic leaves the AP onto the wired network → FortiGate has no visibility
- Traffic from AP1 can reach AP2 through the switch → AP2 delivers it to its client

This is a **fundamental architectural limitation** of Bridge Mode — not a bug.

### The Bridge Mode Solution — Private VLAN

The slide states: *"To prevent this communication, you must have private VLAN enabled, if APs are connected to a FortiSwitch device."*

**Private VLAN (PVLAN)** is a switch-level feature that **isolates ports within the same VLAN**:

```
Normal VLAN:
FortiAP-1 port ──→ Switch ──→ FortiAP-2 port
                      ↑ Ports can communicate ↑

Private VLAN (isolated ports):
FortiAP-1 port ──→ Switch ──→ FortiAP-2 port
                      ↑ Communication BLOCKED at switch level ↑
```

With Private VLAN on FortiSwitch:

- Each AP port is an **isolated port** — can communicate only with the **promiscuous port** (uplink to FortiGate)
- AP ports **cannot communicate with each other** through the switch
- Result: Bridge Mode clients on different APs are now isolated ✅

**Configuration path (FortiSwitch via FortiGate):**

```
WiFi & Switch Controller > FortiSwitch Ports
  → Enable Private VLAN on AP-connected ports
  → Set uplink port as promiscuous
```

### Decision Matrix — Choosing the Right Isolation Method

```
┌──────────────────────────────────────────────────────────────────┐
│            DO YOU NEED CLIENT ISOLATION?                         │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Using TUNNEL MODE SSID?                                         │
│    → Enable Block Intra-SSID Traffic                            │
│    → ✅ Full isolation — all clients, all APs                   │
│                                                                  │
│  Using BRIDGE MODE SSID?                                         │
│    → Enable Block Intra-SSID Traffic                            │
│    → ✅ Isolates clients on the SAME AP                         │
│    → ❌ Clients on DIFFERENT APs can still communicate          │
│                                                                  │
│    Need full isolation in Bridge Mode?                           │
│    → APs on FortiSwitch: Enable Private VLAN                    │
│    → ✅ Full isolation — all clients, all APs                   │
│    → ❌ APs NOT on FortiSwitch: Full isolation not possible      │
│         (consider switching to Tunnel Mode)                      │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```
# 📊 Master Summary

```
┌─────────────────────────────────────────────────────────────────────────┐
│           SSID OPTIONS, GROUPS & CLIENT ISOLATION — SUMMARY             │
├──────────────────────┬──────────────────────────────────────────────────┤
│ FEATURE              │ KEY FACTS                                        │
├──────────────────────┼──────────────────────────────────────────────────┤
│ Max Clients          │ Limits concurrent associations per SSID          │
│                      │ Default = unlimited; set based on radio capacity │
│                      │ Best practice: 25-30 clients per radio           │
├──────────────────────┼──────────────────────────────────────────────────┤
│ Hidden SSID          │ Removes SSID name from beacon frames             │
│                      │ Reduces casual discovery — NOT real security     │
│                      │ Reduces probe request management traffic         │
│                      │ ⚠️ Easily discovered by wireless scanning tools  │
├──────────────────────┼──────────────────────────────────────────────────┤
│ Vendor Beacon IE     │ Embeds AP name/model/serial in beacons           │
│                      │ Used for walk-test site surveys                  │
│                      │ Helps identify APs during post-impl validation   │
├──────────────────────┼──────────────────────────────────────────────────┤
│ SSID Groups          │ Groups multiple SSIDs into one object            │
│                      │ Applied to FortiAP profiles like a single SSID  │
│                      │ Simplifies large-scale multi-SSID management     │
│                      │ ⚠️ Max 8 SSIDs per radio — keep groups lean      │
├──────────────────────┼──────────────────────────────────────────────────┤
│ Block Intra-SSID     │ TUNNEL: Full isolation — all APs, all clients   │
│                      │ BRIDGE: Per-AP only — different AP = NOT blocked │
│                      │ Bridge full isolation → Private VLAN on          │
│                      │ FortiSwitch required                             │
└──────────────────────┴──────────────────────────────────────────────────┘
```

---

### Topic: Wireless Network Access — VLANs with SSIDs

# VLANs with SSIDs Overview

The slide explains **why VLANs are used with SSIDs** and their core benefits:

**The problem — Too many SSIDs:**

- Wireless is a **shared medium** — everyone competes for the same airtime
- More SSIDs = more **management frames** (beacons, probe responses) consuming airtime
- More SSIDs = less available airtime for **actual client data**

**The solution — VLANs on a single SSID:**

| Benefit                           | Explanation                                                    |
|-----------------------------------|----------------------------------------------------------------|
|  **Better performance**         | Fewer SSIDs = less airtime wasted on management frames         |
|  **Network security**           | Logical separation of traffic without physical separation      |
|  **Traffic control**           | Fine-grained control over inter-VLAN communication             |
|  **Broadcast domain reduction** | Smaller broadcast domains = less broadcast traffic per segment |

**Available in:** Both **Tunnel Mode** and **Bridge Mode**

**Standard:** IEEE **802.1Q** defines VLAN tagging

### The Core Problem — SSID Airtime Overhead

Every SSID you broadcast consumes airtime — even when no clients are connected to it:

```
Each SSID broadcasts beacons: ~10 beacons/second
4 SSIDs = 40 beacons/second of overhead
8 SSIDs = 80 beacons/second of overhead
```

In a dense environment (office building with 50 APs), this adds up to thousands of beacon frames per second consuming shared RF spectrum — leaving less capacity for actual user data.

**Without VLANs — typical naive design:**

```
SSID "Corp-WiFi"      → for corporate laptops
SSID "Guest-WiFi"     → for guests
SSID "IoT-WiFi"       → for IoT devices
SSID "Voice-WiFi"     → for VoIP phones
= 4 SSIDs × 10 beacons/sec = 40 mgmt frames/sec per AP
```

**With VLANs on one SSID:**

```
SSID "Corp-WiFi" + VLAN 10 (IT) + VLAN 20 (Finance) + VLAN 30 (HR)
= 1 SSID × 10 beacons/sec = 10 mgmt frames/sec per AP
= 75% reduction in beacon overhead
```

> 💡 **Real-world analogy:** Multiple SSIDs are like having **separate radio stations** for each department — each one broadcasts continuously even when nobody is listening. VLANs on one SSID are like one radio station with **different programme channels** — one broadcast signal, multiple content streams.

### IEEE 802.1Q — VLAN Tagging Standard

**802.1Q** defines how VLAN membership is communicated across a network:

```
Normal Ethernet Frame:
[Dest MAC][Src MAC][EtherType][Payload][FCS]

802.1Q Tagged Frame:
[Dest MAC][Src MAC][802.1Q Tag][EtherType][Payload][FCS]
                        ↑
              4-byte tag containing:
              - TPID: 0x8100 (identifies as 802.1Q)
              - PCP: Priority (CoS — 3 bits)
              - DEI: Drop eligibility (1 bit)
              - VID: VLAN ID (12 bits → supports VLANs 1-4094)
```

**Key requirement from slide:** *"All layer 2 and layer 3 devices along a route must be 802.1Q-compliant to support VLANs along that route."*

This means:

- FortiAP must support 802.1Q tagging (all modern FortiAP models do)
- Switch ports between FortiAP and FortiGate must be configured as **trunks**
- FortiGate must have **VLAN sub-interfaces** configured 

### Broadcast Domain Benefits

**Without VLANs — large broadcast domain:**

```
500 wireless clients → all in one subnet → every broadcast reaches all 500
ARP requests, DHCP renewals, NetBIOS → constant background noise for everyone
```

**With VLANs — smaller broadcast domains:**

```
VLAN 10: 50 IT clients → broadcasts reach only 50 devices
VLAN 20: 100 Finance clients → broadcasts reach only 100 devices
VLAN 30: 350 General staff → broadcasts reach only 350 devices
→ Much less broadcast overhead per client
```
# 📄 PAGE 164 — VLANs with Tunnel Mode

---

## 🖥️ SLIDE SUMMARY

The slide explains how VLANs work in **Tunnel Mode SSIDs**:

**Key architecture:**

- VLANs are tied to the **tunneled SSID interface** on FortiGate
- The SSID interface becomes a **VLAN trunk** (carries multiple tagged VLANs)
- Each VLAN must be configured as a **separate sub-interface** with:
  - Its own **IP address/subnet**
  - Its own **administrative access settings**
  - Its own **DHCP server** (or relay)
- Each VLAN requires **separate firewall policies**

**GUI Paths:**

- VLAN interfaces: `Network > Interfaces`
- Parent interface = SSID interface → create VLAN sub-interfaces under it

---

## 📋 DETAILED INSTRUCTOR NOTES

### How Tunnel Mode VLANs Work — Architecture Deep Dive

In standard Tunnel Mode (no VLANs):

```
Client → FortiAP → [CAPWAP tunnel] → FortiGate SSID interface → single subnet
```

In Tunnel Mode WITH VLANs:

```
Client A (VLAN 10) → FortiAP → [CAPWAP tunnel, tagged VLAN 10] → FortiGate
Client B (VLAN 20) → FortiAP → [CAPWAP tunnel, tagged VLAN 20] → FortiGate

FortiGate SSID interface (trunk):
  ├── VLAN 10 sub-interface → IP: 10.10.10.1/24 → DHCP → Firewall Policy A
  └── VLAN 20 sub-interface → IP: 10.10.20.1/24 → DHCP → Firewall Policy B
```

The SSID interface acts as a **trunk** — it receives VLAN-tagged CAPWAP traffic and distributes it to the correct sub-interface based on the VLAN tag.

---

### Configuring VLAN Sub-Interfaces on FortiGate

**Step 1: Go to Network > Interfaces** Find the SSID interface (e.g., `wlan1` or `Corp-WiFi`)

**Step 2: Create VLAN sub-interface**

```
Interface: Corp-WiFi
  Type: VLAN
  VLAN ID: 10
  IP/Mask: 10.10.10.1/24
  Role: LAN
  Administrative Access: [PING, HTTPS if needed]
  DHCP Server:
    Range: 10.10.10.10-10.10.10.254
    Gateway: 10.10.10.1
    DNS: [your DNS servers]
```

**Repeat** for each additional VLAN (20, 30, etc.)

**Step 3: Create firewall policies per VLAN**

```
Policy: VLAN10-to-Internet
  Source Interface: Corp-WiFi.10  ← VLAN 10 sub-interface
  Destination Interface: wan1
  Source Address: all
  Destination Address: all
  Action: ACCEPT
  Security Profiles: [apply appropriate profiles]
```

---

### Critical Rule — Separate DHCP per VLAN

Each VLAN sub-interface **must** have its own DHCP configuration:

- Different IP subnet per VLAN
- Clients on VLAN 10 get IPs from 10.10.10.0/24
- Clients on VLAN 20 get IPs from 10.10.20.0/24
- They cannot overlap — each is a separate broadcast domain

> ⚠️ **Trainer Warning:** A very common mistake is forgetting to configure DHCP on a new VLAN sub-interface. Clients connect, get tagged into the VLAN, but receive no IP address → they appear connected but have no network access. Always verify DHCP is configured on every VLAN interface.

---

---

# 📄 PAGE 165 — Local Bridge with VLANs

---

## 🖥️ SLIDE SUMMARY

The slide shows **Bridge Mode SSID with VLAN support**:

**Key behavior:**

- FortiAP **tags wireless client traffic** with the correct VLAN ID
- Tagged traffic is placed directly on the **bridged wired network**
- Firewall policies on FortiGate required **only for inter-VLAN routing**
- Local traffic switches at the **FortiSwitch** — never reaches FortiGate
- CAPWAP **control traffic** still goes to FortiGate (management only)

**CLI Configuration:**

```bash
config wireless-controller vap
    edit "SSID-bridge"
        set vdom "root"
        set ssid "SSIDBridge"
        set security wpa3-enterprise
        set auth radius
        set encrypt AES
        set radius-server "FortiAuth"
        set local-bridging enable
    end
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### Bridge Mode + VLANs — How It Differs from Tunnel Mode

**Tunnel Mode + VLANs:**

```
ALL client traffic → CAPWAP tunnel → FortiGate → VLAN sub-interface → inspection → destination
FortiGate sees and inspects EVERYTHING
```

**Bridge Mode + VLANs:**

```
Client traffic → FortiAP tags with VLAN ID → wired network → FortiSwitch routes locally
FortiGate sees ONLY traffic that crosses between VLANs (inter-VLAN routing)
Local intra-VLAN traffic → never reaches FortiGate
```

> 💡 **Real-world analogy:** Bridge Mode VLANs are like a **local post office** that handles all mail within the same city itself — only packages going to another city (another VLAN) get sent to the central sorting facility (FortiGate).

---

### The `local-bridging enable` CLI Command

The key CLI parameter that enables Bridge Mode VLAN behavior:

```bash
set local-bridging enable
```

This tells FortiAP to:

1. Accept wireless client traffic
2. Apply the appropriate VLAN tag based on SSID/RADIUS assignment
3. Forward the tagged traffic directly onto the wired interface (802.1Q trunk)
4. **NOT** send data traffic through CAPWAP to FortiGate

Without this setting → traffic goes through CAPWAP to FortiGate (Tunnel Mode behavior).

---

### When Firewall Policies Are Required in Bridge Mode

The slide states: *"Firewall policies on FortiGate are required only to route traffic between interfaces."*

This means:

- Client in VLAN 10 accessing internet → traffic exits through router/FortiGate WAN interface → **policy needed**
- Client in VLAN 10 accessing server in VLAN 20 → inter-VLAN routing through FortiGate → **policy needed**
- Client in VLAN 10 accessing server also in VLAN 10 → switches locally through FortiSwitch → **NO FortiGate policy needed**

This significantly reduces the number of firewall policies required compared to Tunnel Mode.

---

---

# 📄 PAGE 166 — Local Bridge VLAN Traffic Flow

---

## 🖥️ SLIDE SUMMARY

The slide shows a **detailed traffic flow diagram** for Bridge Mode with multiple VLANs:

```
                    FortiGate
                        |
                   [802.1Q trunk]
                        |
                   FortiSwitch
              ┌─────────┴─────────┐
         FortiAP-1          FortiAP-2
              |
    [Multiple SSIDs with different VLANs]
    VLAN 20 = Finance clients
    VLAN 30 = IT clients
    VLAN 40 = Employee clients
    VLAN 1  = Management traffic (CAPWAP)
```

**Key notes:**

- FortiAP Ethernet interface functions as an **802.1Q trunk**
- Wireless traffic is **bridged locally** and tagged with VLAN ID
- Local VLAN traffic does **NOT tunnel back to FortiGate**
- VLAN IDs are managed **directly in SSID configuration**

---

## �� DETAILED INSTRUCTOR NOTES

### The 802.1Q Trunk on FortiAP

For Bridge Mode VLANs to work, the FortiAP's wired Ethernet port must be connected to a **trunk port** on the switch:

```
FortiAP Ethernet port → [802.1Q trunk] → Switch trunk port
  Carries:
  VLAN 1   (untagged) = Management/CAPWAP
  VLAN 20  (tagged)   = Finance wireless clients
  VLAN 30  (tagged)   = IT wireless clients
  VLAN 40  (tagged)   = Employee wireless clients
```

The switch must be configured to **allow all required VLANs** on the trunk port connected to the FortiAP.

---

### VLAN 1 — Management Traffic Special Role

Notice in the diagram that **VLAN 1 = management traffic (CAPWAP)**:

- The FortiAP's own management traffic (CAPWAP control to FortiGate) travels on the **native/untagged VLAN**
- This is typically VLAN 1 by default
- Client wireless data travels on **tagged VLANs** (20, 30, 40)
- The switch separates these cleanly — management never mixes with user data

> ⚠️ **Security best practice:** Change management VLAN from VLAN 1 to a dedicated management VLAN (e.g., VLAN 99). VLAN 1 is the default on most switches and is a common attack target.

---

### Local Switching — Performance Advantage

The most important performance benefit shown in this diagram:

- Finance client (VLAN 20) → Finance server (also VLAN 20) → **switches locally at FortiSwitch** → never leaves the local network → **maximum throughput, minimum latency**
- In Tunnel Mode, this same traffic would travel: Client → FortiAP → FortiGate (WAN bandwidth consumed) → back to FortiSwitch → Finance server → massive latency and bandwidth waste

> 💡 **Trainer Tip:** For high-throughput local applications (file servers, video production, engineering workstations), Bridge Mode with local VLANs dramatically outperforms Tunnel Mode.

---

---

# 📄 PAGE 167 — Dynamic VLANs

---

## 🖥️ SLIDE SUMMARY

The slide introduces **Dynamic VLANs** — where VLAN assignment is controlled by the RADIUS server:

**Requirements:**

- Only available with **WPA2 Enterprise** or **WPA3 Enterprise** (RADIUS-based authentication)
- RADIUS server must send three specific **IETF attributes** in the Access-Accept reply:

| RADIUS Attribute            | IETF Number | Value to Set                          |
|-----------------------------|-------------|---------------------------------------|
| **Tunnel-Type**             | **IETF 64** | VLAN                                  |
| **Tunnel-Medium-Type**      | **IETF 65** | IEEE 802                              |
| **Tunnel-Private-Group-ID** | **IETF 81** | VLAN ID number or VLAN interface name |

**Supported in:** Both **Tunnel Mode** and **Bridge Mode** SSIDs

---

## 📋 DETAILED INSTRUCTOR NOTES

### What Are Dynamic VLANs?

In **static VLAN** configuration, every client on an SSID gets the same VLAN — or you use multiple SSIDs for different groups.

In **dynamic VLAN** configuration, the RADIUS server decides which VLAN each **individual user** gets — based on their identity:

```
User "john.doe" (IT staff) authenticates via 802.1X
RADIUS Access-Accept reply:
  Tunnel-Type = VLAN
  Tunnel-Medium-Type = IEEE 802
  Tunnel-Private-Group-ID = 30    ← IT VLAN
→ John is placed in VLAN 30

User "jane.smith" (Finance) authenticates via 802.1X
RADIUS Access-Accept reply:
  Tunnel-Private-Group-ID = 20    ← Finance VLAN
→ Jane is placed in VLAN 20

Both on the SAME SSID — completely different VLANs based on identity
```

> 💡 **Real-world analogy:** Dynamic VLANs are like a **hotel room key card** — everyone enters through the same front door (same SSID), but the key card (RADIUS attribute) determines which floor and room they access (which VLAN they join).

---

### The Three IETF RADIUS Attributes — Technical Deep Dive

These are **standard IETF RADIUS attributes** (not Fortinet proprietary):

**IETF 64 — Tunnel-Type = VLAN (value: 13)**

```
Attribute: 64 (Tunnel-Type)
Value: 13 (VLAN)
Purpose: Tells FortiGate "tunnel type information follows — it's VLAN-based"
```

**IETF 65 — Tunnel-Medium-Type = IEEE 802 (value: 6)**

```
Attribute: 65 (Tunnel-Medium-Type)
Value: 6 (IEEE 802)
Purpose: Tells FortiGate "this is IEEE 802 standard — Ethernet/Wi-Fi VLAN assignment"
```

**IETF 81 — Tunnel-Private-Group-ID = \[VLAN ID or name\]**

```
Attribute: 81 (Tunnel-Private-Group-ID)
Value: "101"  ← The actual VLAN ID (or VLAN interface name)
Purpose: The specific VLAN to assign this user to
```

All three must be present in the RADIUS reply — missing any one attribute → dynamic VLAN assignment fails.

---

### RADIUS Server Configuration Example (FortiAuthenticator/NPS)

**For user "john.doe" → VLAN 30:**

```
RADIUS Reply Attributes:
  [64]  Tunnel-Type       = VLAN (13)
  [65]  Tunnel-Medium-Type = IEEE-802 (6)
  [81]  Tunnel-Private-Group-ID = 30
```

**On FortiAuthenticator:** `Authentication > User Management > Local Users` → Edit user → RADIUS Attributes tab → Add the three tunnel attributes

**On Windows NPS:** Network Policy → Settings → RADIUS Attributes → Vendor Specific or Standard Attributes → Add Tunnel attributes

---

### Critical Rule — VLANs Must Pre-Exist on FortiGate

The slide and instructor notes state explicitly: *"FortiGate does not dynamically create VLANs."*

This means:

- Before dynamic VLAN assignment can work, **every possible VLAN** that users might be assigned to **must already exist** as a configured interface on FortiGate
- Firewall policies for each VLAN interface must also be pre-configured
- If RADIUS sends VLAN ID 101 but VLAN 101 doesn't exist on FortiGate → client gets no network access

> ⚠️ **Trainer Warning:** Pre-creating all VLANs is critical and commonly missed. In a deployment with 20 possible VLANs, all 20 must be created with IP, DHCP, and firewall policies BEFORE enabling dynamic VLAN assignment. Missing even one VLAN means some users fail to connect.

---

---

# 📄 PAGE 168 — Dynamic VLAN Assignment RADIUS

---

## 🖥️ SLIDE SUMMARY

The slide shows the **complete dynamic VLAN configuration** with CLI and traffic flow:

**CLI configuration:**

```bash
config wireless-controller vap
    edit <wireless SSID>
        set vlanid 100          # Default VLAN (fallback)
        set dynamic-vlan enable  # Enable dynamic assignment
    end
```

**Traffic flow diagram:**

```
RADIUS server:
  User v1000 → VLAN ID: 101
  User v2000 → VLAN ID: 201

FortiGate (tunnel mode SSID):
  VLAN 101 sub-interface → DHCP server → Users assigned VLAN 101
  VLAN 201 sub-interface → DHCP server → Users assigned VLAN 201

Default VLAN 100 = fallback if RADIUS sends no VLAN info
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### Default VLAN ID — The Fallback Safety Net

The `set vlanid 100` command sets a **default VLAN**:

```
User authenticates successfully via RADIUS
RADIUS Access-Accept received BUT:
  → No Tunnel-Type attribute, OR
  → No Tunnel-Private-Group-ID attribute
  → Dynamic VLAN assignment cannot proceed

FortiGate falls back to default VLAN 100
User placed in VLAN 100 → gets IP from VLAN 100 DHCP pool
```

**When does this happen?**

- RADIUS server misconfiguration — forgot to add VLAN attributes for some users
- New user account created in AD but VLAN policy not yet applied
- Guest users authenticating to a server that doesn't return VLAN attributes

> 💡 **Trainer Tip:** The default VLAN is your safety net AND a diagnostic tool. If many users are landing on the default VLAN unexpectedly, your RADIUS attribute configuration likely has an error.

---

### Tunnel Mode Behavior with Dynamic VLANs

The diagram notes: *"If used on tunnel mode SSID, all traffic is sent to FortiGate."*

Even with dynamic VLAN assignment:

- In **Tunnel Mode** → ALL client traffic goes through CAPWAP to FortiGate → FortiGate routes to correct VLAN sub-interface
- In **Bridge Mode** → FortiAP tags with RADIUS-assigned VLAN → traffic exits locally on the wired network

The dynamic VLAN assignment process is identical in both modes — only the **data path** differs.

---

### Complete Pre-Deployment Checklist for Dynamic VLANs

```
Before enabling dynamic-vlan:
□ All possible VLAN sub-interfaces created on FortiGate
□ Each VLAN has IP address configured
□ Each VLAN has DHCP server or relay configured
□ Each VLAN has at least one firewall policy
□ RADIUS server configured to return all 3 IETF attributes
□ Test with a known user and verify correct VLAN assignment
□ Default VLAN configured as fallback
□ Default VLAN firewall policy and DHCP configured
```

---

---

# 📄 PAGE 169 — VLAN Assignment by Name Tag

---

## 🖥️ SLIDE SUMMARY

The slide introduces **VLAN name-tag assignment** — an alternative to numeric VLAN ID assignment:

**Concept:**

- RADIUS returns a **text string** (name tag) instead of a numeric VLAN ID
- FortiGate matches the string against a **VLAN name table** defined on the SSID
- The name tag maps to one or more VLAN IDs

**Key capability:**

- Each name can map to **1 VLAN ID** (one-to-one) OR up to **8 VLAN IDs** (one-to-many)
- When multiple VLAN IDs are assigned per name → **round-robin** selection

**CLI Configuration:**

```bash
config wireless-controller vap
    edit "wifi_vlan"
        set ssid "wifi_vlan"
        set security wpa2-only-enterprise
        set auth radius
        set radius-server "FAC"
        set schedule "always"
        set dynamic-vlan enable
        config vlan-name
            edit "data"
                set vlan-id 100 200 300   # Three VLANs under one name tag
            next
            edit "infrastructure"
                set vlan-id 213            # Single VLAN under this name tag
        end
    end
```

**GUI Path:** `FortiAuthenticator: Authentication > User Management > Local Users`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Name Tags Instead of VLAN IDs?

**Problem with numeric VLAN IDs:**

- RADIUS admin must know the specific VLAN IDs used on the network
- If VLAN numbering changes → RADIUS configuration must be updated for every user
- Tight coupling between RADIUS config and network VLAN design

**Name tags solve this:**

- RADIUS sends abstract names: `"data"`, `"infrastructure"`, `"guest"`
- The VLAN ID mapping is managed **on FortiGate** — not on RADIUS
- VLAN IDs can change without touching RADIUS configuration
- Separation of concerns: RADIUS team manages user attributes, network team manages VLAN mapping

> 💡 **Real-world analogy:** Name tags are like assigning employees **job titles** (Sales, Engineering, Management) rather than desk numbers. The office layout (VLAN IDs) can change without updating HR records (RADIUS) — you just update the floor plan (VLAN table on FortiGate).

---

### One Name → Multiple VLANs — Round Robin Explained

```bash
edit "data"
    set vlan-id 100 200 300   # Pool of three VLANs
```

When the "data" name tag is returned by RADIUS:

```
User 1 authenticates → assigned VLAN 100 (first in pool)
User 2 authenticates → assigned VLAN 200 (second in pool)
User 3 authenticates → assigned VLAN 300 (third in pool)
User 4 authenticates → assigned VLAN 100 (back to start)
User 5 authenticates → assigned VLAN 200
...and so on
```

**Why use this?** Load balancing across multiple subnets:

- Large "data" user group → spread across 3 VLANs → 3 separate /24 subnets → 3× IP address space
- Each VLAN has its own broadcast domain → reduced broadcast overhead per client
- Max 8 VLAN IDs per name tag

> ⚠️ **Trainer Warning:** Maximum is **8 VLAN IDs per name tag**. Plan your capacity based on expected concurrent users: if you need to support 500 concurrent "data" users and each VLAN can host 254 clients → you need at least 2 VLANs (ideally 3 for headroom).

---

---

# 📄 PAGE 170 — VLAN Pool

---

## 🖥️ SLIDE SUMMARY

The slide introduces **VLAN Pools** — a FortiGate feature for VLAN-based client management:

**Two VLAN pool assignment methods:**

| Method               | How VLANs are Assigned                              | Use Case                                                      |
|----------------------|-----------------------------------------------------|---------------------------------------------------------------|
| **AP FortiAP Group** | VLAN based on which AP group the client connects to | Location-based VLAN (e.g., lobby always → guest VLAN)         |
| **Load Balancing**   | VLAN from pool for load distribution                | Distribute clients across multiple subnets (Tunnel Mode only) |

**Fallback rule:**

- If VLAN pool contains **no valid VLAN ID** → SSID static VLAN ID is used

---

## 📋 DETAILED INSTRUCTOR NOTES

### VLAN Pool vs. Dynamic VLAN — Key Difference

| Feature                     | Dynamic VLAN                | VLAN Pool                             |
|-----------------------------|-----------------------------|---------------------------------------|
| **Who assigns VLAN**        | RADIUS server               | FortiGate (based on AP group or load) |
| **Authentication required** | Yes (WPA2/WPA3 Enterprise)  | No — works with any auth method       |
| **Per-user granularity**    | ✅ Per individual user       | ❌ Per AP group or load balance        |
| **RADIUS dependency**       | ✅ High                      | ❌ None                                |
| **Use case**                | Identity-based segmentation | Location or load-based segmentation   |

---

### AP Group-Based VLAN Assignment — Use Cases

**Use case 1 — Location-based security:**

```
AP Group "Lobby-APs" (reception area)
  → All clients connecting to lobby APs → VLAN 50 (Guest/restricted)
  → Regardless of who they are

AP Group "Office-APs" (inside secure area)
  → All clients connecting to office APs → VLAN 10 (Corporate)
  → Physical location = security context
```

**Use case 2 — Multi-floor segmentation:**

```
AP Group "Floor-1-APs" → VLAN 110 (subnet 10.1.10.0/24)
AP Group "Floor-2-APs" → VLAN 120 (subnet 10.1.20.0/24)
AP Group "Floor-3-APs" → VLAN 130 (subnet 10.1.30.0/24)

Benefits:
→ Smaller broadcast domains per floor
→ Easier troubleshooting (client's IP reveals their floor)
→ Different firewall policies per floor if needed
```

> 💡 **Real-world analogy:** AP group VLAN assignment is like a **building with different colored badges** — if you enter through the main entrance (lobby AP), you get a visitor badge (guest VLAN). If you enter through the employee entrance (office AP), you get an employee badge (corporate VLAN). Your location determines your access.

---

---

# 📄 PAGE 171 — VLAN Pooling and Load Balancing

---

## 🖥️ SLIDE SUMMARY

The slide details the **two VLAN pool load balancing algorithms**:

| Algorithm       | How It Works                                                                  |
|-----------------|-------------------------------------------------------------------------------|
| **Round Robin** | New client → assigned to VLAN with **fewest current clients**                 |
| **Hash**        | New client → assigned based on **hash of (current client count ÷ pool size)** |

**Important restriction:**

- VLAN pooling load balancing is available **only for SSIDs in Tunnel Mode**

**GUI Path:** `WiFi & Switch Controller > SSIDs`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Load Balance Across VLANs?

The problem with large wireless deployments and single subnets:

```
1000 clients → single VLAN 100 → 10.10.100.0/24 → only 254 usable IPs!
→ Cannot fit 1000 clients in a /24 subnet

Solution: VLAN pool with multiple /24 subnets
VLAN 101: 10.10.101.0/24 → 254 clients max
VLAN 102: 10.10.102.0/24 → 254 clients max
VLAN 103: 10.10.103.0/24 → 254 clients max
VLAN 104: 10.10.104.0/24 → 254 clients max
= 1016 clients across 4 VLANs → problem solved
```

Additionally: smaller broadcast domains = less ARP/DHCP/NetBIOS broadcast overhead per client.

---

### Round Robin Algorithm — Deep Dive

```
Initial state:
  VLAN 101: 0 clients
  VLAN 102: 0 clients
  VLAN 103: 0 clients

Client 1 connects → VLAN 101 (was least loaded: 0) → VLAN 101: 1 client
Client 2 connects → VLAN 102 (was least loaded: 0) → VLAN 102: 1 client
Client 3 connects → VLAN 103 (was least loaded: 0) → VLAN 103: 1 client
Client 4 connects → VLAN 101/102/103 (all equal: 1) → VLAN 101: 2 clients
Client 5 connects → VLAN 102 (least: 1) → VLAN 102: 2 clients
```

**Result:** Clients distributed as evenly as possible across VLANs.

**Best for:** Deployments where client count per VLAN matters (approaching subnet capacity limits).

---

### Hash Algorithm — Deep Dive

```
Hash value = f(current total clients on SSID, number of VLANs in pool)

Example with 3 VLANs:
  Total clients = 0  → Hash → VLAN 101
  Total clients = 1  → Hash → VLAN 101
  Total clients = 2  → Hash → VLAN 102
  Total clients = 3  → Hash → VLAN 101
  Total clients = 4  → Hash → VLAN 103
```

**Characteristic of Hash:**

- Distribution may not be perfectly even
- Deterministic — same input always produces same output
- **Faster** to compute than tracking per-VLAN client counts
- Better for **high-throughput environments** where Round Robin tracking overhead matters

**Best for:** Very high-density deployments where computational efficiency matters.

---

### Tunnel Mode Only Restriction — Why?

VLAN pooling load balancing requires **central visibility** of all client counts across all VLANs:

- In **Tunnel Mode**: All traffic flows through FortiGate → FortiGate can count clients per VLAN accurately → load balancing possible ✅
- In **Bridge Mode**: Traffic exits locally at each AP → FortiGate cannot see all clients → cannot accurately count per-VLAN clients → load balancing not possible ❌

This is a fundamental architectural constraint — not a software limitation.

---

---

# 📄 PAGE 172 — VLAN Pooling and FortiAP Groups

---

## 🖥️ SLIDE SUMMARY

The slide explains **FortiAP Group-based VLAN pool assignment**:

**Key concepts:**

- FortiAPs can be organized into **groups** (by floor, section, building, etc.)
- Each AP group is assigned a **specific VLAN** from the pool
- Clients connecting to APs in a group automatically get that group's VLAN

**CLI Configuration:**

```bash
config wireless-controller vap
    edit wlan
        set vlan-pooling wtp-group   # wtp = Wireless Termination Point = FortiAP
        config vlan-pool
            edit 101
                set wtp-group group1  # AP group 1 → VLAN 101
            next
            edit 102
                set wtp-group group2  # AP group 2 → VLAN 102
            next
        end
    end
```

**Important GUI note:**

- FortiGate **automatically creates** VLAN interfaces in the GUI when you define them in the VLAN pool
- However, you must **manually configure** network settings, DHCP, and administrative access for each

---

## 📋 DETAILED INSTRUCTOR NOTES

### FortiAP Groups — What They Are

**FortiAP groups (WTP groups)** are logical collections of FortiAP devices:

- Group APs by **physical location** (floor, building, zone)
- Group APs by **function** (office, conference, lobby, warehouse)
- Groups are used for: VLAN assignment, profile application, firmware upgrade targeting

**Creating a FortiAP group:**

```
WiFi & Switch Controller > Managed FortiAPs
  → Select multiple APs → Group → Create Group
  → Name: "Floor-1-APs"
  → Members: [select specific APs]
```

---

### Location-Based Security Policy — Powerful Use Case

```
Building layout:
  Lobby (AP Group: lobby-group)     → VLAN 50  (Guest — internet only)
  Office floor 1 (AP Group: f1-aps) → VLAN 101 (Corporate — full access)
  Office floor 2 (AP Group: f2-aps) → VLAN 102 (Corporate — full access)
  Server room (AP Group: sr-aps)    → VLAN 200 (Restricted — limited access)

Result:
  A visitor connecting in the lobby → always VLAN 50 (regardless of device)
  An employee on floor 1 → VLAN 101
  The same employee walks to floor 2 → roams → VLAN 102 (location changed)
```

This provides **location-aware network access control** without RADIUS — purely based on physical AP location.

---

### Auto-Created VLANs — The Manual Configuration Requirement

The slide notes: *"FortiGate automatically creates the VLANs without any interface settings such as network, administrative access, DHCP server."*

This means FortiGate creates a **skeleton VLAN interface** (just the VLAN tag) but you must manually add:

```
Network > Interfaces > [Auto-created VLAN]
  IP/Mask: 10.10.101.1/24        ← Must add manually
  Administrative Access: PING    ← Must add manually
  DHCP Server:                   ← Must add manually
    Range: 10.10.101.10-254
    Gateway: 10.10.101.1
```

> ⚠️ **Trainer Warning:** This is a very common post-configuration oversight. After defining VLANs in the pool, always go to `Network > Interfaces` and complete the configuration for each auto-created VLAN. Clients will fail to get IP addresses otherwise.

---

---

# 📄 PAGE 173 — VLAN Pooling, Load Balancing and Zones

---

## 🖥️ SLIDE SUMMARY

The slide explains how FortiGate **automatically organizes load-balancing VLANs into zones**:

**Key behavior:**

- FortiOS **automatically adds** load balancing VLANs to a **zone** based on the SSID
- Zone naming convention: `[SSID-interface-name].zone`
  - Example: SSID interface = `Fortinet` → Zone = `Fortinet.zone`
- All VLANs in the zone are configured with **identical access** automatically
- Zone makes it easier to manage **firewall policies** — one policy covers all VLANs in the zone

**GUI Path:** `Network > Interfaces`

**Still required:**

- Configure each VLAN individually with **subnet, DHCP, and other interface options**

---

## �� DETAILED INSTRUCTOR NOTES

### What Is a FortiGate Zone?

A **zone** is a logical grouping of interfaces on FortiGate:

- Multiple interfaces treated as **one object** in firewall policies
- One firewall policy can cover **all interfaces in the zone** simultaneously
- Changes to zone membership automatically update all policies referencing the zone

---

### Why Zones Are Critical for VLAN Pool Load Balancing

**Without zones — policy nightmare:**

```
If you have 10 load-balancing VLANs (101-110):
  Policy: VLAN101 → Internet → ACCEPT
  Policy: VLAN102 → Internet → ACCEPT
  Policy: VLAN103 → Internet → ACCEPT
  ... × 10 = 10 identical policies to create and maintain
  Adding VLAN 111 → must add another policy manually
```

**With automatic zones:**

```
FortiGate automatically creates: Fortinet.zone
  Zone members: VLAN101, VLAN102, ... VLAN110 (all auto-added)

One firewall policy:
  Source Interface: Fortinet.zone
  Destination: Internet → ACCEPT
  → Covers ALL 10 VLANs with one policy!
  
Adding VLAN 111 to the pool → automatically joins Fortinet.zone
  → Covered by existing policy immediately
```

> 💡 **Real-world analogy:** The zone is like a **department in a company directory** — instead of emailing 50 people individually, you email the "Engineering Department" and everyone in it gets the message. Adding a new engineer to the department automatically includes them in future department emails.

---

### Zone Naming Convention — Practical Example

```
SSID created in FortiGate:
  SSID interface name: Corp-WiFi-SSID
  VLAN Pool created with VLANs 101, 102, 103

FortiGate automatically creates:
  Zone name: Corp-WiFi-SSID.zone
  Zone members: [Corp-WiFi-SSID.101, Corp-WiFi-SSID.102, Corp-WiFi-SSID.103]
```

**Firewall policy using the zone:**

```
Policy: Zone-to-Internet
  Source Interface: Corp-WiFi-SSID.zone    ← All three VLANs covered
  Destination Interface: wan1
  Source Address: all
  Destination Address: all
  Action: ACCEPT
  Security Profiles: [standard corporate profiles]
```

---

### Still Must Configure Each VLAN Individually

Despite the convenience of automatic zones, the slide reminds: *"You must configure each VLAN with its own interface option, such as subnet, DHCP, and so on."*

```
Network > Interfaces:
  Corp-WiFi-SSID.101:
    IP: 10.10.101.1/24
    DHCP: 10.10.101.10 - 10.10.101.254
  
  Corp-WiFi-SSID.102:
    IP: 10.10.102.1/24
    DHCP: 10.10.102.10 - 10.10.102.254
  
  Corp-WiFi-SSID.103:
    IP: 10.10.103.1/24
    DHCP: 10.10.103.10 - 10.10.103.254
```

> ⚠️ **Trainer Warning:** The zone handles **policy management** automatically, but **IP addressing and DHCP** must be configured manually for each VLAN. This is the most common post-deployment issue — VLANs in the zone have no DHCP, clients associate but get APIPA (169.254.x.x) addresses.

---

---

# 📊 Master Summary — Pages 163–173

```
┌──────────────────────────────────────────────────────────────────────────┐
│              VLANs WITH SSIDs — COMPLETE GUIDE                           │
├─────────────────────┬────────────────────────────────────────────────────┤
│ TOPIC               │ KEY FACTS                                          │
├─────────────────────┼────────────────────────────────────────────────────┤
│ Why VLANs on SSIDs  │ Reduce SSID count → less beacon overhead           │
│                     │ Smaller broadcast domains → better performance      │
│                     │ Logical segmentation → better security              │
│                     │ IEEE 802.1Q standard — all devices must support     │
├─────────────────────┼────────────────────────────────────────────────────┤
│ Tunnel Mode VLANs   │ SSID interface = VLAN trunk                        │
│                     │ Each VLAN = separate sub-interface on FortiGate     │
│                     │ Each VLAN needs: IP, DHCP, firewall policy          │
│                     │ All traffic inspected by FortiGate                  │
├─────────────────────┼────────────────────────────────────────────────────┤
│ Bridge Mode VLANs   │ FortiAP tags traffic with VLAN ID                  │
│                     │ Traffic exits locally — not tunneled to FortiGate   │
│                     │ FortiAP port must be 802.1Q trunk to switch         │
│                     │ FortiGate policies only for inter-VLAN routing      │
├─────────────────────┼────────────────────────────────────────────────────┤
│ Dynamic VLANs       │ RADIUS assigns VLAN per authenticated user          │
│                     │ Requires WPA2/WPA3 Enterprise                       │
│                     │ 3 IETF attributes: [64]=VLAN [65]=802 [81]=VLAN-ID │
│                     │ All VLANs must pre-exist on FortiGate               │
│                     │ Default VLAN = fallback if no RADIUS VLAN attr      │
├─────────────────────┼────────────────────────────────────────────────────┤
│ VLAN Name Tags      │ RADIUS sends text string → maps to VLAN ID(s)      │
│                     │ Up to 8 VLAN IDs per name → round-robin assignment  │
│                     │ Decouples RADIUS config from VLAN numbering         │
├─────────────────────┼────────────────────────────────────────────────────┤
│ VLAN Pool           │ AP Group method: location-based VLAN assignment     │
│                     │ Load Balance: Round Robin or Hash                   │
│                     │ Load balancing: Tunnel Mode ONLY                    │
│                     │ Auto VLANs created → manual IP/DHCP still required  │
├─────────────────────┼────────────────────────────────────────────────────┤
│ Zones               │ Load balancing VLANs auto-grouped into zone         │
│                     │ Zone name: [SSID-interface].zone                    │
│                     │ One firewall policy covers all zone VLANs           │
│                     │ Still need per-VLAN IP/DHCP configuration           │
└─────────────────────┴────────────────────────────────────────────────────┘

⚠️  CRITICAL RULES:
  1. FortiGate NEVER auto-creates VLANs dynamically from RADIUS
  2. Every VLAN needs IP + DHCP + firewall policy — no exceptions
  3. VLAN load balancing = Tunnel Mode ONLY
  4. Dynamic VLANs = WPA2/WPA3 Enterprise ONLY (RADIUS required)
  5. Bridge Mode VLANs need 802.1Q trunk on switch port to FortiAP
```

---

### Topic: Wireless Network Access — Security Fabric Quarantine & Automated Response

---

---

# 📄 PAGE 185 — Automated Response — Quarantine

---

## 🖥️ SLIDE SUMMARY

The slide introduces the **Security Fabric's automated quarantine capability**:

**How the Security Fabric enables automated response:**

- Multiple devices **share and leverage threat information** across the entire fabric
- If a device **fails an IOC (Indicator of Compromise) check** → the entire fabric reacts
- Switches and APs can **automatically quarantine** the device at the access layer

**Why it matters:**

| Scenario                        | Result Without Quarantine         | Result With Quarantine                              |
|---------------------------------|-----------------------------------|-----------------------------------------------------|
| 🤖 **Compromised IoT device**   | Spreads malware across network    | Instantly isolated at access layer                  |
| 🧑‍💻 **Infected guest device** | Can attack other guests/corporate | Automatically isolated + remediation option offered |

**GUI Path:** `Security Fabric > Physical Topology`

---

## 📋 DETAILED INSTRUCTOR NOTES

### What Is the Security Fabric?

The **Fortinet Security Fabric** is the overarching architecture that connects all Fortinet products into a **unified, communicating ecosystem**:

```
Security Fabric Members:
  FortiGate (firewall + wireless controller)
       ↕ shares threat intelligence
  FortiSwitch (access layer switching)
       ↕ shares threat intelligence
  FortiAP (wireless access points)
       ↕ shares threat intelligence
  FortiAnalyzer (log analysis + IOC detection)
       ↕ shares threat intelligence
  FortiEDR/FortiClient (endpoint protection)
```

When one member detects a threat → ALL members can act on it simultaneously.

> 💡 **Real-world analogy:** The Security Fabric is like a **hospital's internal communication system** — when one doctor identifies a contagious patient, the entire hospital is immediately informed, isolation rooms are prepared, and all staff take protective measures — automatically, within seconds.

---

### IOC — Indicator of Compromise — Explained

An **IOC (Indicator of Compromise)** is a piece of forensic evidence that suggests a device has been compromised:

**Examples of IOCs:**

| IOC Type                   | Example                                         |
|----------------------------|-------------------------------------------------|
| **Known malicious IP**     | Device communicates with a botnet C&C server    |
| **Known malicious domain** | Device resolves a malware distribution domain   |
| **Suspicious URL pattern** | Device accesses phishing site structure         |
| **Malware hash**           | File on device matches known malware signature  |
| **Unusual behavior**       | Device suddenly scans ports on internal network |

**How FortiAnalyzer detects IOCs:**

- Receives logs from FortiGate (web filter blocks, IPS events, DNS queries, traffic logs)
- Compares against **FortiGuard IOC database** — continuously updated threat intelligence
- If a match is found → **IOC verdict** is issued against that device's IP/MAC

---

### Why Access Layer Quarantine Is Critical

Traditional security approaches quarantine at **Layer 3** (block IP in firewall):

```
Traditional quarantine:
  Device IP blocked at firewall → device still on network
  → Can still attack other devices on the same L2 segment
  → Can still spread malware via ARP poisoning, DHCP attacks
  → Can still communicate with devices on same subnet (bypasses L3 firewall)
```

**Access Layer Quarantine** acts at **Layer 2** — at the switch port or AP association:

```
Security Fabric quarantine:
  Device's MAC address blocked at FortiSwitch port / FortiAP association
  → Device completely isolated from ALL network access
  → Cannot communicate with ANY other device (wired or wireless)
  → Attacker has no foothold to pivot from
```

> 💡 **Real-world analogy:** Layer 3 quarantine is like **changing the combination on the safe** — the thief is still inside the building and can cause damage. Access Layer quarantine is like **physically removing the thief from the building** — complete isolation immediately.

---

### IoT Device Quarantine — Specific Importance

IoT devices are particularly vulnerable because:

- They often run **outdated firmware** — rarely updated
- Many have **no endpoint security** (no antivirus, no EDR)
- They are **always on** — prime targets for compromise
- Once compromised → can act as **pivot points** to attack other network segments

With Security Fabric quarantine:

1. FortiAnalyzer detects IOC on IoT device (e.g., DNS query to C&C server)
2. Security Fabric automatically quarantines the device at the AP level
3. Device is isolated — cannot communicate with any other device
4. Alert sent to administrator for remediation

---

---

# 📄 PAGE 186 — Security Fabric Quarantine Automation

---

## 🖥️ SLIDE SUMMARY

The slide shows the **complete 6-step quarantine automation workflow**:

```
Step 1: Workstation tries to access malicious site
         ↓
Step 2: FortiGate detects and blocks traffic (UTM/Web Filter)
         ↓
Step 3: FortiGate sends log record to FortiAnalyzer
         ↓
Step 4: FortiAnalyzer processes logs using IOC services (detection engine)
         ↓
Step 5: FortiAnalyzer determines security risk verdict
         → sends verdict back to FortiGate
         ↓
Step 6: FortiGate automation stitch quarantines the compromised workstation
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### Step-by-Step Analysis — Full Technical Detail

#### Step 1 — Malicious Site Access Attempt

A workstation (could be wired or wireless) attempts to:

- Visit a **known malware distribution site**
- Contact a **botnet Command & Control (C&C) server**
- Access a **phishing page**
- Download a **file with known malware hash**

The key word is **"attempts"** — the activity doesn't need to succeed to trigger the workflow.

---

#### Step 2 — UTM Detection and Blocking

FortiGate's **UTM (Unified Threat Management)** engines examine the traffic:

```
Traffic from workstation:
  → Web Filter profile: URL matches FortiGuard malicious category → BLOCK
  → DNS Filter: Domain matches known C&C → BLOCK
  → IPS engine: Exploit signature detected → BLOCK
  → Antivirus: File hash matches malware → BLOCK
```

FortiGate blocks the traffic AND **generates a log entry** for every event.

---

#### Step 3 — Log Forwarding to FortiAnalyzer

FortiGate sends **real-time log streams** to FortiAnalyzer:

- Web filter logs (URL violations)
- IPS logs (attack signatures)
- Traffic logs (allowed/denied flows)
- DNS filter logs (malicious domain queries)

FortiAnalyzer is the **brain** of the IOC detection system — it aggregates all logs from all FortiGates in the fabric.

---

#### Step 4 — IOC Processing by FortiAnalyzer

FortiAnalyzer's **threat detection engine** processes logs:

```
FortiAnalyzer receives logs:
  Log: "Workstation 10.10.1.50 attempted DNS query to evil-c2.com"
       ↓
  FortiGuard IOC database query:
    "evil-c2.com" → MATCH: Known C&C server for Botnet-XYZ
       ↓
  IOC verdict: Workstation 10.10.1.50 is COMPROMISED
```

FortiGuard continuously updates the IOC database with:

- Known C&C server IPs and domains
- Malware distribution URLs
- Botnet signatures
- Exploit kit indicators

---

#### Step 5 — Verdict Sent Back to FortiGate

FortiAnalyzer sends the **IOC verdict** to the root FortiGate of the Security Fabric:

```
FortiAnalyzer → FortiGate API call:
  Device: MAC aa:bb:cc:dd:ee:ff (IP 10.10.1.50)
  Verdict: COMPROMISED
  IOC: Known C&C communication
  Confidence: HIGH
  Action: Recommended quarantine
```

This is an **active feedback loop** — FortiAnalyzer doesn't just log, it actively instructs FortiGate to respond.

---

#### Step 6 — Automation Stitch Executes Quarantine

FortiGate receives the IOC verdict → **automation stitch triggers**:

```
Trigger: IOC verdict from FortiAnalyzer
Action: Quarantine MAC address aa:bb:cc:dd:ee:ff

Result:
  Wired device: FortiSwitch port blocked (Layer 2 isolation)
  Wireless device: FortiAP disassociates device → moves to quarantine VLAN
```

The entire flow from **Step 1 to Step 6** can happen in **seconds** — far faster than any human response.

> 💡 **Trainer Tip:** This automated response is especially valuable **outside business hours** — when no IT staff are watching dashboards. The system detects and contains threats at 3 AM without any human intervention.

---

---

# 📄 PAGE 187 — Security Fabric Automation Stitch

---

## 🖥️ SLIDE SUMMARY

The slide shows the **automation stitch configuration** in FortiGate:

**Automation stitch components:**

| Component   | Value in Example                                                    |
|-------------|---------------------------------------------------------------------|
| **Trigger** | Compromised host detected by IOC from FortiAnalyzer                 |
| **Action**  | Quarantine the MAC address to block traffic from compromised device |

**GUI Path:** `Security Fabric > Automation`

**Key technical notes from slide:**

- Wireless compromised hosts treated **identically** to wired hosts
- FortiAnalyzer sends IOC verdict to the **root FortiGate** of the Security Fabric group
- **Access Layer Quarantine** = Layer 2 action — places host in isolation
- Automation stitches configured **per-device** — cannot use FortiManager templates

---

## 📋 DETAILED INSTRUCTOR NOTES

### What Is an Automation Stitch?

An **automation stitch** is FortiGate's **if-this-then-that** engine:

```
IF [Trigger condition occurs]
THEN [Execute one or more Actions]
```

Think of it like a **security playbook automated** in software — no human needs to manually execute response steps.

---

### Anatomy of a Quarantine Automation Stitch

**Trigger options for quarantine:**

```
Security Fabric > Automation > Create New Stitch
  Trigger: IOC Detection (from FortiAnalyzer)
    Conditions:
      - Minimum severity: High
      - IOC type: Any (or specific: C&C, Botnet, Malware)
```

**Action options:**

```
  Action: Access Layer Quarantine
    - Quarantine by MAC address
    - Duration: [permanent / time-limited]
    - Scope: [local FortiGate / entire Security Fabric]
```

**Additional available actions:**

- Send email/SMS alert to administrator
- Create ServiceNow/ITSM ticket automatically
- Run a script
- Trigger another FortiGate policy change

---

### Why Root FortiGate Receives the Verdict

In a Security Fabric with multiple FortiGates (branch + HQ):

```
Security Fabric topology:
  Root FortiGate (HQ) ← FortiAnalyzer sends IOC verdict here
    ↓ Security Fabric communication
  Branch FortiGate-1 (connected to compromised device)
    ↓
  FortiSwitch/FortiAP (executes access layer quarantine)
```

FortiAnalyzer always sends to the **root** because:

- Root has visibility into all fabric members
- Root can propagate quarantine commands to all downstream devices
- Centralized control ensures consistent response across the entire fabric

---

### Per-Device Configuration Limitation

The slide notes: *"You apply the setting on a per-device basis, it cannot be applied using a template."*

This means:

- If you have 10 branch FortiGates → you must configure the automation stitch on each one individually
- FortiManager templates cannot push automation stitch configurations
- Plan for this operational overhead in large multi-site deployments

> ⚠️ **Trainer Warning:** In large deployments with many FortiGates, the per-device limitation means significant manual effort to deploy automation stitches consistently. Consider using **FortiManager device-level configuration** (not templates) to push consistent automation stitch settings.

---

### Wired vs. Wireless Quarantine — Same Stitch, Different Action

The same automation stitch works for both:

```
Wired device compromised:
  Stitch triggers → FortiSwitch receives quarantine command
  → Switch port placed in quarantine VLAN
  → Device isolated at Layer 2

Wireless device compromised:
  Stitch triggers → FortiAP receives quarantine command
  → AP disassociates client from current SSID
  → Client reassociated to quarantine SSID/VLAN
  → Device isolated at Layer 2
```

Both are **Layer 2 access layer actions** — the device loses network connectivity immediately.

---

---

# 📄 PAGE 188 — Integrated Wireless Quarantine

---

## 🖥️ SLIDE SUMMARY

The slide details the **wireless-specific quarantine configuration**:

**Two quarantine methods:**

- 🤖 **Automatic** — via Security Fabric automation stitch
- 👤 **Manual** — administrator-initiated

**Critical requirements:**

- Only works with **Tunnel Mode SSIDs** ❌ Bridge Mode not supported
- Must be part of a **Security Fabric** (with FortiAnalyzer)

**What FortiGate auto-creates when quarantine is enabled on an SSID:**

| Auto-Created Resource          | Purpose                                          |
|--------------------------------|--------------------------------------------------|
| 🔀 **Quarantine soft switch**  | Virtual switch that isolates quarantined devices |
| 🌐 **Quarantine interface**    | Dedicated sub-interface for quarantined clients  |
| 🖥️ **Default captive portal** | Web page shown to quarantined users              |

**What is NOT auto-created:**

- ❌ **No quarantine firewall policy** — must be created manually
- Filtering occurs at **FortiGate level only** — not at AP level

**GUI Path:** `WiFi & Switch Controller > SSIDs`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Tunnel Mode Only?

The Tunnel Mode requirement for wireless quarantine is architectural:

```
Tunnel Mode quarantine flow:
  Compromised client → FortiAP → CAPWAP tunnel → FortiGate
  FortiGate moves client to quarantine VLAN → client gets quarantine IP
  FortiGate shows captive portal → client sees "You are quarantined"
  FortiGate controls ALL traffic → complete isolation enforced centrally
```

```
Bridge Mode — why it doesn't work:
  Compromised client → FortiAP → Local network (bypasses FortiGate)
  FortiGate cannot intercept local traffic → cannot redirect to captive portal
  Cannot enforce quarantine VLAN at FortiGate level
  → Quarantine enforcement not possible
```

> ⚠️ **Design implication:** If wireless quarantine is a security requirement for your deployment, you **must** use Tunnel Mode SSIDs. This is a critical design decision that must be made before deployment — remember, traffic mode cannot be changed after SSID creation.

---

### Auto-Created Resources — Deep Dive

When you enable quarantine on a Tunnel Mode SSID, FortiGate automatically creates:

#### 1. Quarantine Soft Switch

```
A "soft switch" is a virtual Layer 2 switch inside FortiGate:
  quarantine-softswitch
    Members: [quarantine-interface, SSID quarantine sub-interface]
    Purpose: Connects quarantined devices to the captive portal
    No internet access by default
```

#### 2. Quarantine Interface

```
  quarantine-interface:
    IP: 192.168.200.1/24 (or similar — auto-assigned)
    DHCP: Auto-configured for quarantine clients
    Purpose: Provides IP to quarantined wireless clients
```

#### 3. Default Captive Portal

```
  Replacement message displayed to quarantined users:
    Title: "Your device has been quarantined"
    Message: "Your device has been isolated due to a security threat.
              Please contact IT support for assistance."
    
  Fully customizable:
    System > Replacement Messages > WiFi > Quarantine
```

---

### What Quarantined Wireless Client Experiences

From the **user's perspective**:

1. Browsing normally → suddenly all websites redirect to a warning page
2. Page says: "Your device has been quarantined for security reasons"
3. Possibly a link to self-remediation steps (if policy allows)
4. Device cannot reach any other network resource
5. Must contact IT to be released from quarantine

From the **network perspective**:

```
Before quarantine:
  Client MAC: aa:bb:cc:dd:ee:ff → SSID "Corp-WiFi" → VLAN 10 → Full access

After quarantine:
  Client MAC: aa:bb:cc:dd:ee:ff → Quarantine SSID → Quarantine VLAN 200
  → Only access: captive portal page
  → All other traffic: DROPPED
```

---

### The Missing Firewall Policy — Why It Matters

The slide states: *"No quarantine firewall policy by default."*

This means after quarantine is enabled and the device is moved to the quarantine VLAN:

- The device has an IP address (from quarantine DHCP)
- The device is shown the captive portal
- But it has **absolutely no internet access** — not even to download a security tool

**For basic quarantine (no remediation):** This is fine — block everything, user calls IT.

**For self-remediation quarantine:** You must create limited firewall policies:

```
Policy: Quarantine-Remediation
  Source Interface: quarantine-interface
  Source: quarantine-subnet
  Destination: wan1
  Service: DNS (UDP 53), HTTPS (TCP 443)
  Destination Address: [antivirus vendor update servers only]
  Action: ACCEPT
  → Allows user to update antivirus and remove malware
  → No general internet access
```

> 💡 **Trainer Tip:** Always define a remediation policy for your quarantine VLAN. Isolating users without giving them a path to fix their device leads to frustrated users calling IT for every quarantine event. Self-remediation reduces IT workload significantly.

---

---

# 📄 PAGE 189 — Integrated Wireless Quarantine (Continued)

---

## 🖥️ SLIDE SUMMARY

The slide shows the **FortiGate interfaces and firewall policies** automatically created and manually required for quarantine:

**Network > Interfaces shows (auto-created):**

- Quarantine soft switch interface
- Quarantine sub-interface with DHCP server
- Captive portal automatically linked

**Policy & Objects > Firewall Policy shows (manually created examples):**

- Limited access policies to allow **DNS resolution**
- Limited HTTPS access to **specific remediation resources**
- Purpose: Allow quarantined devices to update/patch themselves

---

## 📋 DETAILED INSTRUCTOR NOTES

### Auto-Created Interface Architecture

When quarantine is enabled on SSID "Corp-WiFi" (for example):

```
Network > Interfaces:

  Corp-WiFi (SSID interface — Tunnel Mode)
  ├── Corp-WiFi.10 (VLAN 10 — normal clients)
  └── Corp-WiFi.quarantine (quarantine sub-interface — auto-created)
          ↓
      quarantine-softswitch (auto-created)
          IP: 192.168.200.1/24
          DHCP: 192.168.200.10 - 192.168.200.254
          Captive Portal: enabled (shows quarantine message)
```

---

### Auto-Created DHCP Server for Quarantine

The quarantine DHCP server is configured automatically with:

- Private IP range (e.g., 192.168.200.0/24)
- Short lease time (typically 1 hour) — to reclaim IPs quickly
- Gateway: quarantine interface IP
- DNS: typically FortiGate itself (for DNS filtering in quarantine)

Quarantined clients get an IP → can receive the captive portal page → but nothing else.

---

### Manually Created Remediation Policies — Practical Examples

**Policy 1 — Allow DNS:**

```
Source: quarantine-interface
Destination: wan1
Service: DNS (UDP/TCP 53)
Action: ACCEPT
Purpose: Allow domain resolution for update servers
```

**Policy 2 — Allow Antivirus Updates:**

```
Source: quarantine-interface
Destination: wan1
Service: HTTPS (TCP 443)
Destination Address: [Windows Update IPs, vendor AV update URLs]
Action: ACCEPT
URL Filter: Allow only known update domains
Purpose: Allow Windows Update, antivirus updates
```

**Policy 3 — Allow HTTP for Update Tools:**

```
Source: quarantine-interface
Destination: wan1
Service: HTTP (TCP 80)
Destination Address: [specific remediation tool download servers]
Action: ACCEPT
Purpose: Allow download of security cleaning tools
```

> ⚠️ **Important security note:** Be very specific with remediation policies. The purpose is remediation — not general internet access. Use **URL filtering** to restrict HTTPS traffic to only known update/remediation domains. A quarantined compromised device with general internet access could continue malicious activity.

---

### Captive Portal Customization for Quarantine

The default quarantine message can be fully customized:

```
System > Replacement Messages > WiFi > Quarantine

Customize to include:
  → Company logo
  → Clear explanation of why device was quarantined
  → Step-by-step self-remediation instructions
  → IT help desk contact number/email
  → Link to self-service remediation tool download
  → Estimated resolution time
```

A well-designed quarantine page **reduces IT support calls** and speeds up remediation.

---

---

# 📄 PAGE 190 — Manual Client Quarantine

---

## 🖥️ SLIDE SUMMARY

The slide explains **manual quarantine** — administrator-initiated isolation:

**Two ways to manually quarantine a wireless client:**

| Method                       | Path                                                                      |
|------------------------------|---------------------------------------------------------------------------|
| **Security Fabric topology** | `Security Fabric > Physical Topology` → right-click device → Quarantine   |
| **FortiView**                | `Dashboard > WiFi > Clients By FortiAP` → right-click client → Quarantine |

**What happens after quarantine:**

- Client moved to **quarantine VLAN** immediately
- Client shown **captive portal** (quarantine message)

**Managing quarantined hosts:**

| Task                         | GUI Path                                       |
|------------------------------|------------------------------------------------|
| View all quarantined devices | `Dashboard > Users & Devices > Quarantine`     |
| View by assets               | `Dashboard > Assets & Identities > Quarantine` |
| Release single device        | Select device → Remove from quarantine         |
| Release all devices          | Remove All button                              |

---

## 📋 DETAILED INSTRUCTOR NOTES

### When Manual Quarantine Is Needed

While **automated quarantine** (via IOC + FortiAnalyzer) handles most scenarios, manual quarantine is needed when:

**Scenario 1 — Pre-emptive isolation:**

```
IT receives a report: "User John's laptop is acting strange"
Admin suspects infection but automated IOC hasn't triggered yet
Admin manually quarantines John's MAC → investigation proceeds
→ Prevent potential spread before automated detection catches up
```

**Scenario 2 — Policy violation response:**

```
User deliberately violating acceptable use policy
(e.g., torrenting, accessing prohibited content)
Automated systems may not flag this as an IOC
Admin manually quarantines as a disciplinary/policy action
```

**Scenario 3 — Incident response:**

```
Active security incident in progress
Attacker's device identified on wireless network
Admin needs immediate isolation faster than waiting for IOC workflow
Manual quarantine provides instant response
```

**Scenario 4 — No FortiAnalyzer deployed:**

```
Environment doesn't have FortiAnalyzer
Automated IOC-based quarantine not available
Manual quarantine is the only quarantine option available
```

---

### Manual Quarantine via Security Fabric Physical Topology

The `Security Fabric > Physical Topology` view shows:

```
Visual map of the entire Security Fabric:
  FortiGate (root)
    ├── FortiSwitch-1
    │     ├── Wired client A
    │     └── Wired client B
    └── FortiAP-1
          ├── Wireless client X  ← right-click → Quarantine
          └── Wireless client Y
```

Right-clicking a device shows:

- **Quarantine** — isolate immediately
- **Unquarantine** — release from isolation
- **Ban** — permanently block MAC

This gives a visual, intuitive way to quarantine specific devices without needing to know their IP/MAC.

---

### Manual Quarantine via FortiView

`Dashboard > WiFi > Clients By FortiAP` shows:

```
Table view of all connected wireless clients:
  | Client Name | MAC | IP | SSID | AP | Signal | Usage |
  |-------------|-----|----|----|------|--------|-------|
  | JohnPC      | aa:bb:.. | 10.10.1.50 | Corp | AP-1 | -65dBm | 150MB |
  | GuestPhone  | cc:dd:.. | 10.10.2.30 | Guest | AP-2 | -72dBm | 5MB |
```

Right-click any client → **Quarantine** → immediately isolated.

---

### Managing Quarantined Hosts — Dashboard Widget

The `Dashboard > Users & Devices > Quarantine` widget shows:

```
Current Quarantine Status:
  ┌──────────────────────────────────────────────────────┐
  │ MAC: aa:bb:cc:dd:ee:ff │ IP: 10.10.1.50 │ Reason: IOC │
  │ Name: JohnPC           │ Time: 14:32:01  │ Type: Auto  │
  │                        │                 │ [Remove]    │
  ├──────────────────────────────────────────────────────┤
  │ MAC: cc:dd:ee:ff:11:22 │ IP: 10.10.1.75 │ Reason: Manual│
  │ Name: GuestDevice      │ Time: 15:10:45  │ Type: Manual│
  │                        │                 │ [Remove]    │
  └──────────────────────────────────────────────────────┘
  [Remove All]
```

**Key capabilities:**

- See **who is quarantined** and for how long
- See **why they were quarantined** (IOC type or "manual")
- **Release individual devices** after remediation
- **Release all devices** (e.g., after a false-positive mass quarantine)

---

### Release From Quarantine — Process Best Practice

When releasing a device from quarantine, follow this process:

```
Step 1: Verify remediation
  → Confirm antivirus scan completed
  → Confirm malware removed
  → Confirm system patches applied

Step 2: Document
  → Record device MAC, user, IOC that triggered quarantine
  → Record remediation actions taken
  → Record who approved the release

Step 3: Release from quarantine
  Dashboard > Users & Devices > Quarantine → [Remove]

Step 4: Monitor
  → Watch device activity for 24 hours post-release
  → Verify no repeat IOC triggers
  → Escalate to re-quarantine if suspicious activity resumes
```

> ⚠️ **Trainer Warning:** Never release a device from quarantine without **confirming remediation**. Releasing an unpatched compromised device immediately re-exposes the network to the same threat that triggered quarantine.

---

---

# 📊 Master Summary — Pages 185–190

```
┌──────────────────────────────────────────────────────────────────────────┐
│        SECURITY FABRIC WIRELESS QUARANTINE — COMPLETE GUIDE              │
├──────────────────────────┬───────────────────────────────────────────────┤
│ TOPIC                    │ KEY FACTS                                     │
├──────────────────────────┼───────────────────────────────────────────────┤
│ IOC Detection            │ FortiAnalyzer compares logs vs FortiGuard IOC │
│                          │ database → issues verdict on compromised host  │
│                          │ Examples: C&C comms, malware hash, botnet DNS │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Quarantine Workflow      │ 1. Device tries malicious access              │
│ (6 Steps)                │ 2. FortiGate UTM detects/blocks               │
│                          │ 3. Logs sent to FortiAnalyzer                 │
│                          │ 4. FortiAnalyzer processes IOC                │
│                          │ 5. Verdict sent to root FortiGate             │
│                          │ 6. Automation stitch quarantines device       │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Automation Stitch        │ Trigger: IOC verdict from FortiAnalyzer       │
│                          │ Action: Quarantine MAC (L2 access layer)      │
│                          │ ⚠️ Per-device config — no FortiManager template│
│                          │ Wired → FortiSwitch port isolation            │
│                          │ Wireless → FortiAP disassociation + VLAN move │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Wireless Quarantine      │ Tunnel Mode ONLY — Bridge Mode not supported  │
│ Requirements             │ Must be in Security Fabric with FortiAnalyzer │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Auto-Created Resources   │ ✅ Quarantine soft switch                     │
│ (when SSID quarantine    │ ✅ Quarantine interface + DHCP                │
│  is enabled)             │ ✅ Default captive portal                     │
│                          │ ❌ NO firewall policy (must create manually)  │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Manual Quarantine        │ Via: Security Fabric Physical Topology        │
│                          │ Via: Dashboard > WiFi > Clients By FortiAP   │
│                          │ Manage at: Dashboard > Users & Devices >      │
│                          │            Quarantine                         │
│                          │ Release: Per-device or Remove All             │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Remediation Policy       │ Must create manually — not auto-created       │
│                          │ Allow: DNS + HTTPS to update servers only     │
│                          │ Block: General internet access                │
│                          │ Purpose: Allow self-remediation of device     │
└──────────────────────────┴───────────────────────────────────────────────┘

⚠️  CRITICAL RULES TO REMEMBER:
  1. Wireless quarantine = TUNNEL MODE ONLY — design for this early
  2. FortiAnalyzer is REQUIRED for automated IOC-based quarantine
  3. No quarantine firewall policy is auto-created — ALWAYS create manually
  4. Automation stitches are per-device — no FortiManager template support
  5. Never release from quarantine without confirming remediation
  6. Access Layer Quarantine = Layer 2 isolation — strongest possible containment
```

---

---

### Topic: FortiAP Profiles — Configuration, Radio Settings & Channel Management

---

---

# 📄 PAGE 197 — Access Points (FortiAP Profile Overview)

---

## 🖥️ SLIDE SUMMARY

The slide introduces **FortiAP as a thin AP** and explains the purpose of **AP profiles**:

**What is a FortiAP?**

- A **thin AP** — requires a wireless controller to function
- Controlled by either **FortiGate** or **FortiLAN Cloud**

**What is an AP Profile?**

| AP Profile Defines             | Examples                    |
|--------------------------------|-----------------------------|
| **Operating band**             | 802.11g, 802.11ac, 802.11ax |
| **Channels and band settings** | Channel 1/6/11 on 2.4 GHz   |
| **SSIDs to broadcast**         | Corp-WiFi, Guest-WiFi       |

**Model-specific:**

- Each FortiAP model has a **different AP profile**
- **Platform setting** defines which AP model the profile supports

**CLI terminology:** AP profile = **WTP (Wireless Termination Point) profile** in FortiOS CLI

---

## 📋 DETAILED INSTRUCTOR NOTES

### Thin AP vs. Fat AP — The Architecture Difference

**Fat AP (autonomous):**

```
Fat AP contains:
  - Full wireless configuration locally stored
  - Encryption/decryption engine
  - Firewall rules
  - Authentication logic
  - QoS rules
  - Works independently — no controller needed
  ❌ Difficult to manage at scale (configure each AP individually)
  ❌ No centralized visibility
```

**Thin AP (FortiAP):**

```
FortiAP contains:
  - Basic radio hardware
  - CAPWAP client (connects to controller)
  - Minimal local intelligence
  ✅ All intelligence lives in FortiGate
  ✅ Change one profile → instantly updates hundreds of APs
  ✅ Centralized visibility and control
  ✅ Zero-touch deployment
```

> 💡 **Real-world analogy:** A fat AP is like a **standalone printer** — it has its own settings that must be configured locally. A thin AP is like a **network printer** — all settings are pushed from a central print server. Change the server setting → all printers update automatically.

---

### Why AP Profiles Are Model-Specific

Different FortiAP models have different:

- **Number of radios** (2-radio vs. 3-radio models)
- **Supported bands** (2.4/5 GHz vs. 2.4/5/6 GHz)
- **Maximum transmit power** (varies by hardware)
- **Antenna configuration** (internal vs. external)
- **PoE requirements** (PoE vs. PoE+ vs. passive PoE)

FortiGate reads the AP's **platform type** from the profile and only pushes settings that the AP hardware can actually support:

```
FortiAP-231F (2-radio, 802.11ax):
  Profile allows: 2.4 GHz + 5 GHz
  Profile ignores: 6 GHz settings (hardware doesn't support)

FortiAP-443K (3-radio, 802.11be):
  Profile allows: 2.4 GHz + 5 GHz + 6 GHz + Wi-Fi 7 features
  All settings applicable
```

> ⚠️ **Trainer Warning:** If you assign a profile designed for a 3-radio AP to a 2-radio AP, FortiGate automatically removes the third radio settings. This is safe but can cause confusion — always verify profile-to-model compatibility.

---

---

# 📄 PAGE 198 — Access Point Profile (Default Assignment)

---

## 🖥️ SLIDE SUMMARY

The slide explains **automatic default profile assignment**:

**Default profile behavior:**

- FortiGate **automatically assigns** a default AP profile to newly discovered APs
- Default profile name format: `[AP-Model]-default` (e.g., `FAP231F-default`)
- AP remains **unauthorized** until manually approved — no config pushed yet
- After authorization → status changes to **green icon** → CAPWAP tunnel established

**GUI Path:** `WiFi & Switch Controller > Managed FortiAPs`

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Authorization Gate — Why It Exists

FortiGate does NOT automatically trust every FortiAP that connects:

```
FortiAP connects → CAPWAP discovery → FortiGate lists it as "pending"
                                               ↓
                                    No config pushed yet
                                    No SSID broadcast yet
                                    AP in "unauthorized" state (gray icon)
                                               ↓
                              Admin reviews → Authorizes manually
                                               ↓
                                    Config pushed → AP goes green
                                    SSIDs broadcast → clients can connect
```

**Why this security gate matters:**

- Prevents rogue APs from automatically joining your managed network
- A physical attacker cannot plug in a FortiAP and have it automatically receive corporate configuration
- Admin must explicitly approve each AP — providing an audit trail

---

### Default Profile Naming Convention

When FortiGate discovers a new FortiAP, it:

1. Reads the AP's **model identifier** from the CAPWAP discovery message
2. Looks for an existing profile named `[model]-default`
3. If found → assigns it; if not found → creates a generic default

**Examples:**

```
FortiAP-231F discovered → profile assigned: FAP231F-default
FortiAP-443K discovered → profile assigned: FAP443K-default
FortiAP-U431F discovered → profile assigned: FAPU431F-default
```

The default profile contains **safe, basic settings**:

- Both radios enabled as Access Points
- All allowed channels for the configured country
- No SSIDs assigned (must add SSIDs to profile)
- Transmit power at default values

---

---

# 📄 PAGE 199 — Authorized FortiAP Devices

---

## 🖥️ SLIDE SUMMARY

After authorization, the GUI enables the following actions on each AP:

| Action                    | How                                                    |
|---------------------------|--------------------------------------------------------|
| **Change AP status**      | Authorized ↔ Deauthorized                              |
| **Upgrade AP firmware**   | Right-click → Upgrade → FortiOS finds correct firmware |
| **Assign custom profile** | Change from default to custom AP profile               |
| **Restart FortiAP**       | Remote reboot via GUI                                  |
| **Telnet to FortiAP CLI** | Direct CLI access for troubleshooting                  |

**Icon meanings:**

- 🟢 **Green icon** = Authorized + CAPWAP tunnel active = Online
- ⚫ **Gray icon** = Not authorized OR CAPWAP tunnel down = Offline/pending

---

## 📋 DETAILED INSTRUCTOR NOTES

### Firmware Upgrade via GUI — Requirements

The slide notes: *"This option requires you to register FortiAP on the Fortinet support site and have a valid support contract."*

**What happens during a GUI firmware upgrade:**

1. Admin right-clicks AP → Upgrade
2. FortiGate contacts **FortiGuard** to find the correct firmware for that AP model
3. Firmware downloaded to FortiGate
4. FortiGate pushes firmware to FortiAP via CAPWAP data channel
5. FortiAP writes firmware to flash, reboots
6. AP comes back online with new firmware, re-establishes CAPWAP

> ⚠️ **Trainer Warning:** The AP **reboots** during firmware upgrade → brief wireless outage on that AP. Always upgrade during maintenance windows or use **Hitless Rolling Upgrade** (covered on Page 87) to minimize impact.

---

### Telnet Access to FortiAP CLI

The Telnet option in the GUI opens a **direct CLI session** to the FortiAP:

- Useful for **advanced troubleshooting** — checking AP-level logs, radio stats, CAPWAP status
- Not recommended for routine configuration — use the profile instead
- Telnet is unencrypted — only use over trusted management networks

**Common FortiAP CLI debug commands:**

```bash
cw_debug all     # Enable all debug output
cfg -s           # Show all AP configuration
iwconfig         # Show radio interface stats
tcpdump -i eth0  # Packet capture on AP wired interface
```

---

---

# 📄 PAGE 200 — Preauthorizing FortiAP Devices

---

## 🖥️ SLIDE SUMMARY

The slide explains **preauthorization** — adding FortiAPs to FortiGate **before they physically arrive**:

**CLI syntax for preauthorization:**

```bash
config wireless-controller wtp
    edit FP231FTF23032211    # AP serial number
    # FortiGate auto-assigns default profile based on serial
    # wtp-profile: FAP231F-default (auto-assigned)
end
```

**Key behavior:**

- You must add APs **one by one** using serial numbers
- Can assign a **pre-configured profile** or let default be assigned
- When AP physically connects → **automatically authorized** (no manual approval needed)
- FortiGate immediately pushes configuration via CAPWAP

**GUI Path:** `WiFi & Switch Controller > Managed APs`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Preauthorization Is Valuable

**Standard deployment (no preauthorization):**

```
1. AP ships to site
2. Technician plugs in AP
3. Admin notified "new AP pending authorization"
4. Admin logs in → finds AP in list → authorizes
5. Admin assigns correct profile
6. Config pushed → AP online
= Multiple manual steps AFTER AP arrives on site
```

**With preauthorization (zero-touch deployment):**

```
1. Admin enters AP serial numbers + profile assignments BEFORE shipping
2. AP ships to site
3. Technician plugs in AP
4. AP connects → FortiGate recognizes serial → AUTOMATICALLY authorizes
5. Config immediately pushed → AP online within minutes
= Zero manual steps needed after AP arrives on site
= True zero-touch deployment
```

> 💡 **Real-world analogy:** Preauthorization is like adding an employee to the HR system **before their first day** — when they arrive, their badge is already ready, their computer account already exists, and they can start working immediately.

---

### Serial Number — Where to Find It

The AP serial number is:

- Printed on the **physical label** on the AP (bottom or side)
- Available in the **FortiCloud asset management** system
- Included in the **purchase order** from Fortinet

**Serial number format for FortiAP:**

```
FP = FortiAP
231 = Model number (231F series)
F = Suffix (Wi-Fi 6)
TF23032211 = Unique identifier
→ Full serial: FP231FTF23032211
```

---

### What Happens If AP Connects Without Preauthorization

If an AP connects and is NOT in the preauthorized list:

- It appears in `Managed FortiAPs` as **"pending"**
- Default profile is assigned based on model
- Admin must manually authorize it

With preauthorization → the pending step is eliminated.

---

---

# 📄 PAGE 201 — Provisioning FortiAP Devices

---

## 🖥️ SLIDE SUMMARY

The slide explains the **two levels of FortiAP configuration**:

| Level               | Scope                                 | CLI Context                              | GUI                   |
|---------------------|---------------------------------------|------------------------------------------|-----------------------|
| **AP Profile**      | Applies to ALL APs using that profile | `config wireless-controller wtp-profile` | FortiAP Profiles page |
| **Per-AP Override** | Applies to ONE specific AP only       | `config wireless-controller wtp`         | Limited — mainly CLI  |

**Key rules:**

- One AP → can use **only ONE profile** (cannot assign multiple profiles to one AP)
- One profile → can be assigned to **multiple APs**
- Per-AP overrides available **CLI only** (not full GUI support)

---

## 📋 DETAILED INSTRUCTOR NOTES

### AP Profile vs. Per-AP Override — When to Use Each

**Use AP Profile (standard approach):**

```
Scenario: 50 APs on office floors — all same model, same settings
Solution: One profile "Office-Floor-Profile" → applied to all 50 APs
Benefit: Change one setting in profile → all 50 APs updated instantly
```

**Use Per-AP Override (exception handling):**

```
Scenario: One AP in a conference room needs lower TX power than others
Profile: "Office-Floor-Profile" → TX power 17 dBm (for all APs)
Per-AP override on conference room AP: TX power 10 dBm (for this AP only)
Benefit: All other settings inherited from profile, only TX power overridden
```

> 💡 **Real-world analogy:** AP profiles are like **company dress code policy** — everyone follows the same rules. Per-AP overrides are like a **medical accommodation** — one employee gets an exception to the dress code for a specific reason, while everyone else follows the standard policy.

---

### Per-AP Override CLI Syntax

```bash
config wireless-controller wtp
    edit FP231FTF23032211    # Specific AP serial number
        config radio-1
            set override-txpower enable
            set auto-power-high 14       # Override TX power for this AP only
            set auto-power-low 10
        end
    end
```

**Available per-AP override options:**

- Transmit power
- Channel
- Band
- SSID (add/remove specific SSIDs)

> ⚠️ **Trainer Warning:** Per-AP overrides can create management complexity at scale. Document all overrides carefully — when troubleshooting, an AP behaving differently from its profile counterparts is almost always caused by undocumented per-AP overrides.

---

---

# 📄 PAGE 202 — Custom FortiAP Profile

---

## 🖥️ SLIDE SUMMARY

The slide shows the **custom FortiAP profile creation** page with key settings:

**Platform settings:**

| Setting            | Options                 | Notes                              |
|--------------------|-------------------------|------------------------------------|
| **AP Platform**    | \[Select model\]        | Must match physical hardware       |
| **Platform mode**  | Single 5GHz / Dual 5GHz | Wi-Fi 6E only — dual 5GHz possible |
| **Country/Region** | \[Select country\]      | Default = United States            |
| **Dedicated scan** | Enable/Disable          | 3-radio APs only                   |

**Additional settings:**

- **Password management** — inherits from WTP global setting or set custom
- **Direct AP access** — HTTPS/SSH access to AP device
- ⚠️ Set a password if enabling direct access

**GUI Path:** `WiFi & Switch Controller > FortiAP Profiles`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Platform Mode — Single vs. Dual 5 GHz

**Standard mode (all FortiAP models):**

```
Radio 1: 2.4 GHz (802.11ax/n/g/b)
Radio 2: 5 GHz (802.11ax/ac/n/a)
Radio 3: Scanning (if available) — Wi-Fi 6/6E models
```

**Dual 5 GHz mode (Wi-Fi 6E FortiAP only):**

```
Radio 1: 5 GHz (802.11ax/ac/n/a)
Radio 2: 5 GHz (802.11ax/ac/n/a)
Radio 3: 6 GHz (802.11ax) — or scanning
```

**Why choose Dual 5 GHz?**

- High-density environments where 5 GHz throughput is needed on two separate channels
- Splitting client load across two 5 GHz radios
- Trade-off: No dedicated radio for scanning in Dual 5G mode

> ⚠️ **Critical trade-off:** If you enable Dual 5 GHz mode, you **cannot** have a dedicated Radio 3 for RF/wireless scanning. Choose based on your priority: throughput (Dual 5G) vs. security monitoring (scanning radio).

---

### Country/Region — Why It's Critical

Radio regulations differ by country:

| Country           | 2.4 GHz Channels | 5 GHz Channels     | Max TX Power |
|-------------------|------------------|--------------------|--------------|
| **USA**           | 1-11             | Up to 23           | 30 dBm       |
| **Europe (ETSI)** | 1-13             | Varies (DFS rules) | 20 dBm       |
| **Japan**         | 1-14             | Varies             | 20 dBm       |
| **Australia**     | 1-13             | Varies             | 30 dBm       |

Setting the **wrong country** causes:

- AP may use channels **not legal in your region** → regulatory violation → possible fines
- AP may use channels that interfere with government/military radar (DFS channels)
- AP may transmit at power levels **above legal limits**

> ⚠️ **Trainer Warning:** Always set the correct Country/Region before deploying FortiAPs. This is especially important for international deployments — an AP shipped from the US and deployed in Germany must have its country setting changed to Germany.

---

### Dedicated Scan Radio — Security Monitoring

For Wi-Fi 6 and Wi-Fi 6E FortiAPs with 3 radios:

**With dedicated scan enabled:**

```
Radio 1: 2.4 GHz — serves clients
Radio 2: 5 GHz — serves clients
Radio 3: DEDICATED SCAN — 24/7 RF monitoring + WIDS
  → Scans all channels continuously
  → Detects rogue APs
  → Detects wireless attacks
  → Collects spectrum data
  → Never serves clients — monitoring only
```

**Without dedicated scan:**

```
Radio 3: Normal Access Point — serves clients
RF monitoring: Only during background scan gaps (not continuous)
```

> 💡 **Trainer Tip:** In security-sensitive environments (finance, healthcare, government), **always enable dedicated scan** on 3-radio APs. The continuous monitoring capability of a dedicated radio is far superior to background scanning on a client-serving radio.

---

---

# 📄 PAGE 203 — Custom FortiAP Profile (Continued)

---

## 🖥️ SLIDE SUMMARY

The slide covers additional custom profile settings:

**Direct AP Access:**

- **Default:** After FortiGate manages an AP → direct HTTPS/SSH access is **disabled**
- Can re-enable via profile or per-AP setting
- **Not recommended** but useful for troubleshooting
- Password options:
  - **Set** — manually configure a password
  - **Leave Unchanged** — keeps last configured password

**Client Load Balancing:**

- **Frequency Handoff** — steer 2.4 GHz-capable clients to 5 GHz
- **AP Handoff** — steer overloaded AP clients to nearby less-loaded APs

**802.1X Supplicant mode:**

- FortiAP can act as **802.1X supplicant** (client) when connected to a switch with 802.1X port authentication
- Supports: **EAP-FAST, EAP-TLS, EAP-PEAP**

---

## �� DETAILED INSTRUCTOR NOTES

### Client Load Balancing — Two Methods Explained

#### Frequency Handoff (Band Steering)

**Problem:** Many clients prefer 2.4 GHz (older default) even when 5 GHz is available and much better.

**How frequency handoff works:**

```
Client sends probe request on 2.4 GHz
AP detects client also supports 5 GHz (from client capabilities)
AP temporarily ignores the 2.4 GHz probe
Client retries → AP responds on 5 GHz instead
Client connects on 5 GHz

Result: Client gets 5 GHz performance → better speed + less interference
```

**Why 5 GHz is better for capable clients:**

- More available channels → less congestion
- Higher throughput (802.11ac/ax)
- Less interference (fewer competing devices)

> ⚠️ **Caution:** Some legacy devices (IoT, older laptops) may not handle frequency handoff well. Test in your environment before enabling broadly.

---

#### AP Handoff (Load Balancing Between APs)

**Problem:** One AP is overloaded (50+ clients) while an adjacent AP has only 5 clients.

**How AP handoff works:**

```
Client associates with AP-1 (overloaded: 50 clients)
AP-1 detects it's at capacity threshold
AP-1 sends disassociation request to client
Client re-associates → scans → finds AP-2 (lightly loaded: 5 clients)
Client connects to AP-2

Result: Load balanced across both APs → better performance for all clients
```

> 💡 **Real-world analogy:** Frequency handoff is like a hotel desk clerk steering a guest from the noisy ground-floor room (2.4 GHz) to a quieter room upstairs (5 GHz). AP handoff is like directing guests to a check-in counter with a shorter queue when one is overloaded.

---

### 802.1X Supplicant Mode — Important Concept

Normally in 802.1X:

- **Supplicant** = client device (laptop, phone)
- **Authenticator** = FortiAP or switch
- **Authentication server** = RADIUS

**FortiAP as 802.1X supplicant** = FortiAP itself authenticates to the switch:

```
FortiSwitch port with 802.1X enabled
     ↕
FortiAP (acting as 802.1X supplicant)
  → Presents credentials (EAP-TLS cert, or EAP-PEAP username/password)
  → FortiSwitch forwards to RADIUS
  → RADIUS validates → Access-Accept
  → FortiSwitch opens port → CAPWAP traffic flows to FortiGate
```

**When to use this:**

- Network policy requires **every device** on the network to authenticate — including APs
- Prevents rogue devices from plugging into switch ports
- Compliance requirement in high-security environments (PCI-DSS Zone boundaries, healthcare)

---

### Password Management — Security Implication

**Default behavior (managed AP):**

- FortiGate manages the AP → direct HTTPS/SSH login disabled
- AP is accessible only through FortiGate's management plane
- More secure — no direct attack surface on the AP itself

**When you re-enable direct access:**

- Set a strong password — default is blank (huge security risk)
- The password is pushed via the AP profile → consistent across all APs using that profile
- **Leave Unchanged** is dangerous if the previous password was blank or weak

> ⚠️ **Security best practice:** If direct AP access must be enabled (e.g., for troubleshooting), set a strong password, document it securely, and disable direct access again after troubleshooting is complete.

---

---

# 📄 PAGE 204 — Custom FortiAP Profile — LAN Port

---

## 🖥️ SLIDE SUMMARY

The slide explains **FortiAP LAN port functionality**:

**Port overview:**

| Port         | Present On          | Purpose                                           |
|--------------|---------------------|---------------------------------------------------|
| **WAN port** | ALL FortiAP models  | CAPWAP communication to FortiGate/FortiLAN Cloud  |
| **LAN port** | Some FortiAP models | Extends wired network access to connected devices |

**LAN port bridging options:**

| Mode                | Behavior                                                   | IP Source                                           |
|---------------------|------------------------------------------------------------|-----------------------------------------------------|
| **Bridged to LAN**  | AP acts as hub — wired + wireless on same subnet           | IP from WAN directly (same range as AP)             |
| **Bridged to SSID** | AP acts as switch — wired and wireless on same SSID subnet | IP from DHCP server on wired network via controller |

---

## 📋 DETAILED INSTRUCTOR NOTES

### LAN Port Use Cases — Real-World Scenarios

**Scenario 1 — Office desk with no wired port:**

```
Office layout: AP mounted on ceiling, employee desk below
Employee has desktop PC (no Wi-Fi)
LAN port bridge: PC plugs into AP's LAN port
AP bridges PC traffic with the SSID → PC gets network access
No additional switch port or cable run needed
```

**Scenario 2 — Conference room:**

```
Conference room has ceiling AP and a table with presentations
HDMI receiver, video conferencing unit (wired Ethernet only)
LAN port bridge: Devices plug into AP LAN port
All devices share SSID network access through the AP
```

---

### Bridge to LAN vs. Bridge to SSID — Key Difference

**Bridge to LAN:**

```
AP WAN port → wired network (FortiGate)
AP LAN port → PC/device
Traffic path: PC → AP LAN → AP WAN → wired network
IP: PC gets IP from the same DHCP as the AP management IP
Network: Same broadcast domain as AP management
```

**Bridge to SSID:**

```
AP WAN port → FortiGate
AP LAN port → PC/device
AP radio → wireless clients
Traffic path: PC → AP LAN → tunneled with SSID traffic → FortiGate
IP: PC gets IP from SSID's DHCP pool
Network: Same subnet as wireless clients on that SSID
```

> 💡 **Trainer Tip:** Bridge to SSID is more common in enterprise — it puts wired-AP-connected devices into the same VLAN/policy as the SSID wireless clients. Bridge to LAN is simpler but provides less segmentation control.

---

---

# 📄 PAGE 205 — Radio Settings in the FortiAP Profile

---

## 🖥️ SLIDE SUMMARY

The slide lists all **configurable radio settings** per radio in the AP profile:

**Radio modes:**

| Mode                  | Description                                |
|-----------------------|--------------------------------------------|
| **Disabled**          | Radio turned off entirely                  |
| **Access Point**      | Normal operation — serves wireless clients |
| **Dedicated Monitor** | Monitors RF only — no client connections   |

**Full list of radio settings:**

| Setting                         | Options/Details                                                      |
|---------------------------------|----------------------------------------------------------------------|
| **WIDS profile**                | Wireless Intrusion Detection System settings                         |
| **Radio resource provision**    | DARRP — automatic channel selection                                  |
| **Band**                        | 2.4 GHz (ax/n/g/b) / 5 GHz (ax/ac/n/a) / 6 GHz (ax)                  |
| **Channel width**               | 20 / 40 / 80 / 160 MHz                                               |
| **Channels**                    | Based on Country/Region setting                                      |
| **Short Guard Interval**        | Disabled default; enable for marginally better 802.11ac/n throughput |
| **Transmit power mode**         | Percent / dBm / Auto                                                 |
| **Transmit power**              | Specific value or auto range                                         |
| **SSIDs**                       | Tunnel / Bridge / Manual (mix)                                       |
| **Monitor channel utilization** | Tracks airtime usage per channel                                     |

**2.4 GHz channel plan note:**

- Only **Three Channels (1, 6, 11)** or **Four Channels (1, 4, 8, 11)** available
- No other combination provides true non-overlapping coverage

---

## 📋 DETAILED INSTRUCTOR NOTES

### Radio Mode — Access Point vs. Dedicated Monitor

**Access Point mode** (default):

- Radio serves wireless clients
- Broadcasts SSIDs
- Accepts client associations
- Can do background scanning during gaps (limited monitoring)

**Dedicated Monitor mode:**

- Radio serves **no clients** — SSID broadcasting disabled
- Continuously scans ALL channels in rotation
- Detects: rogue APs, evil twin APs, wireless attacks, neighboring networks
- Feeds data to WIDS (Wireless Intrusion Detection System)
- Provides complete **RF environment awareness**

> �� **When to use Dedicated Monitor:** In environments where wireless security monitoring is a compliance requirement — PCI-DSS requires wireless IDS/IPS. With a 3-radio AP, Radio 3 in dedicated monitor mode satisfies this requirement without impacting client capacity.

---

### Channel Width — Impact on Throughput and Interference

```
Channel Width   Throughput      Interference Risk   Use Case
─────────────────────────────────────────────────────────────
20 MHz         Low-Medium      Lowest              Dense environments, 2.4 GHz
40 MHz         Medium          Low                 General office use
80 MHz         High            Medium              Standard 5 GHz enterprise
160 MHz        Very High       High                High-throughput 5/6 GHz
```

**Key insight:** Wider channels = more throughput BUT more chances of overlapping with neighboring networks and more interference from DFS radar detection.

> 💡 **Real-world analogy:** Channel width is like the number of lanes on a highway — more lanes (wider channel) = more cars (data) can travel simultaneously, but construction on more lanes also disrupts more traffic.

---

### Short Guard Interval (SGI) — Technical Explanation

**Guard interval** = the dead time between successive OFDM symbols to prevent inter-symbol interference:

```
Normal guard interval: 800 ns
Short guard interval:  400 ns (saves time)
```

**Impact:**

- SGI provides approximately **11% throughput improvement** in ideal conditions
- Only beneficial in **low-interference, short-range** environments
- In environments with multipath interference (reflections), SGI can **reduce reliability**
- Default is disabled — enable only after testing in your specific environment

---

### SSID Assignment in Radio Settings

Three SSID assignment modes:

- **Tunnel** — shows only Tunnel Mode SSIDs
- **Bridge** — shows only Bridge Mode SSIDs
- **Manual** — allows mixing Tunnel AND Bridge SSIDs on same radio

> ⚠️ **Best practice:** Avoid mixing Tunnel and Bridge SSIDs on the same radio where possible. It complicates troubleshooting and can cause unexpected traffic behavior.

---

---

# 📄 PAGE 206 — Channels

---

## 🖥️ SLIDE SUMMARY

The slide shows the **channel configuration UI** for all three bands:

**Key channel facts:**

| Band        | Standard            | Max Channels (USA)    | Notes                             |
|-------------|---------------------|-----------------------|-----------------------------------|
| **2.4 GHz** | 802.11b/g/n/ax      | 11 (USA) / 13 (EU)    | Limited non-overlapping: 1, 6, 11 |
| **5 GHz**   | 802.11a/n/ac/ax     | Up to 23 (UNII bands) | Some marked with \* = DFS         |
| **6 GHz**   | 802.11ax (Wi-Fi 6E) | Many new channels     | New technology, not all countries |

**DFS (Dynamic Frequency Selection):**

- Channels marked with **asterisk (\*)** in 5 GHz band
- Subject to radar detection requirements

**GUI allows:**

- Manual channel selection, OR
- Setting a **channel plan** (FortiGate selects from plan)

---

## 📋 DETAILED INSTRUCTOR NOTES

### Understanding DFS Channels — Critical Knowledge

**DFS = Dynamic Frequency Selection** — a regulatory requirement for 5 GHz:

**Why DFS exists:**

- Some 5 GHz channels overlap with **weather radar, military radar, and airport radar** frequencies
- Regulations require Wi-Fi devices to detect radar signals and **immediately vacate** the channel

**How DFS works:**

```
AP starts on DFS channel (e.g., channel 100)
AP listens for 60 seconds (Channel Availability Check)
No radar detected → AP starts broadcasting
         ↓
RADAR DETECTED during operation:
AP must stop transmitting within 200ms
AP must vacate channel for 30 minutes minimum
AP switches to a non-DFS channel or another DFS channel
All clients must re-associate → brief service interruption
```

**Impact in enterprise deployments:**

- Near airports or military bases → DFS events can be frequent
- Each DFS event causes **client disconnection** (30+ seconds of disruption)
- In environments with frequent DFS triggers → **avoid DFS channels** in AP profile
- Remove DFS channels from the selectable channel list in your profile

> ⚠️ **Trainer Warning:** DFS channel events in enterprise environments are a common source of mysterious wireless connectivity complaints. Users report "Wi-Fi drops for about a minute every few hours." Always investigate DFS event logs when troubleshooting intermittent wireless issues near airports or industrial areas.

---

### 2.4 GHz — The Non-Overlapping Channel Reality

```
2.4 GHz spectrum (USA): 2.412 - 2.472 GHz
11 channels, each 22 MHz wide, spaced 5 MHz apart

Visual representation:
Ch 1  |----22MHz----|
Ch 2       |----22MHz----|
Ch 3            |----22MHz----|
...
Ch 6                              |----22MHz----|
Ch 11                                                 |----22MHz----|

Only channels 1, 6, and 11 have NO overlap with each other
→ Maximum 3 APs can operate in same area without co-channel interference
```

The slide mentions a 4-channel plan (1, 4, 8, 11):

- This is sometimes used in very dense deployments
- Channels 4 and 8 slightly overlap with 1/6 and 6/11 but reduce co-channel interference in ultra-dense scenarios
- Not ideal — the 3-channel plan (1, 6, 11) is almost always preferred

---

---

# 📄 PAGE 207 — Channel Selection

---

## 🖥️ SLIDE SUMMARY

The slide explains **channel selection principles** and introduces **DARRP**:

**Key principles:**

- Channel selection varies by **region regulations**
- Channel width **exceeds spacing** → adjacent channel overlap is inevitable
- Solution: Use channels **every 4th or 5th** to avoid overlap (1, 6, 11 on 2.4 GHz)

**Band comparison:**

| Band        | Interference Level      | Range  | Obstacle Penetration | Max Channels      |
|-------------|-------------------------|--------|----------------------|-------------------|
| **2.4 GHz** | High (crowded)          | Long   | Good                 | 3 non-overlapping |
| **5 GHz**   | Medium                  | Medium | Moderate             | Up to 23          |
| **6 GHz**   | Low (new, less devices) | Short  | Limited              | Many new          |

**Two selection methods:**

- **Manual** — admin selects specific channels
- **DARRP** — FortiGate automatically selects best channel per AP

---

## 📋 DETAILED INSTRUCTOR NOTES

### 5 GHz — Why It's Preferred for Enterprise

**More channels:**

- US: up to 23 non-overlapping channels in 5 GHz vs. only 3 in 2.4 GHz
- More APs can operate in the same area without interfering

**Less congestion:**

- Consumer devices (baby monitors, microwaves, Bluetooth) operate mainly in 2.4 GHz
- 5 GHz has fewer competing devices

**Higher throughput:**

- 802.11ac and 802.11ax (Wi-Fi 6) achieve maximum speeds on 5 GHz
- Wider channels (80/160 MHz) possible without interference

**Trade-off:**

- Shorter wavelength → shorter range (typically 30-50% less range than 2.4 GHz)
- Higher frequency → worse obstacle penetration (walls, floors absorb more)

---

### 6 GHz — The Future of Wireless

**The new 6 GHz band (Wi-Fi 6E/7):**

```
6 GHz: 5.925 - 7.125 GHz → 1,200 MHz of new spectrum
Compared to:
  2.4 GHz: only 100 MHz available
  5 GHz: approximately 500 MHz available
```

**Advantages:**

- **Seven 160 MHz channels** in 6 GHz vs. two in 5 GHz
- Extremely low congestion (new band — few devices yet)
- Very high throughput potential
- Wi-Fi 6E/7 capable devices required (newer devices only)

**Current limitation:** Not yet available in all countries (regulatory approval still in progress in some regions).

---

---

# 📄 PAGE 208 — DARRP

---

## 🖥️ SLIDE SUMMARY

The slide introduces **DARRP (Distributed Automatic Radio Resource Provisioning)**:

**What DARRP does:**

- Each FortiAP **independently selects the best Wi-Fi channel** based on scan results
- Evaluates: BSSID/RSSI data, channel bandwidth, interference sources

**DARRP behavior:**

- Re-evaluates channel selection every **24 hours (86,400 seconds)**
- Can be overridden via **DARRP profile**
- **Clients automatically signaled** to migrate when channel changes
- Reduces **"chatter"** (unnecessary communication) between FortiAP devices

**Cautions:**

- Can cause issues if the network is **at full capacity** during channel change
- DARRP scan doesn't detect **intermittent interference** (e.g., microwave ovens)
- Configure **schedule** in DARRP profile to control when scans run

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why DARRP Exists — The Manual Channel Problem

In a large deployment with 100 APs:

**Without DARRP (manual channels):**

```
Initial deployment: Admin assigns channels 1, 6, 11 in rotation
3 months later: Neighbor company installs 20 APs on channel 6
All FortiAPs on channel 6 now experience severe interference
Admin must manually survey entire building → reassign channels
= Hours of work + service disruption
```

**With DARRP:**

```
Neighbor company installs new APs on channel 6
DARRP detects increased interference on channel 6 at next optimization cycle
DARRP automatically moves affected APs to less congested channels
= Zero admin intervention required
```

> 💡 **Real-world analogy:** DARRP is like **traffic routing software** (GPS navigation) — when traffic congestion appears, it automatically reroutes to clearer roads without the driver needing to do anything.

---

### DARRP Timing — Why 24 Hours?

**Default: 86,400 seconds (24 hours)**

Why not more frequent?

- Channel changes cause **brief client disruption** (clients must re-associate)
- Changing channels every hour would create constant disruption
- RF environment changes relatively slowly — daily optimization is sufficient

**When to reduce the interval:**

- Very dynamic RF environment (many competing networks appearing/disappearing)
- Temporary events (conferences, trade shows) where RF environment changes rapidly

**When to increase the interval or disable:**

- Very stable RF environment — channel changes cause unnecessary disruption
- Small deployments where manual channel assignment is more predictable

---

### Intermittent Interference — DARRP's Limitation

DARRP does **not** detect intermittent interference sources because:

- It only scans at optimization time (every 24 hours by default)
- A microwave oven that operates for 2 minutes every few hours will NOT be in operation during DARRP's scan
- DARRP sees a clean channel → assigns it → microwave starts → interference occurs

**Solution for intermittent interference:**

- Use a **dedicated monitoring radio** (Radio 3) for continuous spectrum analysis
- FortiAP with dedicated scan radio provides real-time spectrum analysis between DARRP cycles

---

---

# 📄 PAGE 209 — DARRP Algorithm

---

## 🖥️ SLIDE SUMMARY

The slide explains **how DARRP selects channels** using a two-phase algorithm:

**Inputs DARRP considers:**

- **Neighbor channel configuration** (which channels neighboring APs use)
- **Non-Wi-Fi interference sources** (radar, microwave, other RF sources)
- **Channel utilization** (how busy each channel is)

**Uses weights and thresholds** to fine-tune selection

**Two-phase process:**

| Phase       | What Happens                                                                        |
|-------------|-------------------------------------------------------------------------------------|
| **Phase 1** | Reviews parameters, calculates weights (noise floor, channel load)                  |
| **Phase 2** | Monitoring stage — checks if AP exceeds thresholds for TX retries or receive errors |

**Example diagram from slide:**

```
AP1: Channel 1, Power 10dBm
AP2: Channel 6, Power 15dBm
AP3: Channel 11, Power 10dBm
→ Each AP independently selected cleanest available channel
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### DARRP Phase 1 — Weighted Parameter Calculation

In Phase 1, DARRP collects and weights the following measurements:

| Parameter                          | What It Measures                      | Default Weight |
|------------------------------------|---------------------------------------|----------------|
| **Noise floor**                    | Background RF noise level (dBm)       | High weight    |
| **Channel load**                   | % of time channel is busy (0-100%)    | High weight    |
| **Neighboring AP count**           | How many BSSIDs heard on each channel | Medium weight  |
| **Neighboring AP signal strength** | RSSI of each neighboring BSSID        | Medium weight  |
| **Non-Wi-Fi interference**         | Energy from non-802.11 sources        | High weight    |

**Scoring:**

- Each candidate channel receives a **score** based on weighted parameters
- Channel with the **lowest interference score** is selected
- This ensures the AP picks the cleanest channel — not just any empty channel

---

### DARRP Phase 2 — Threshold Monitoring

After channel selection, Phase 2 **continuously monitors** the selected channel:

**TX retry threshold:**

```
FortiAP tracks: How often it must retransmit packets
Normal: < 5% retry rate
Threshold exceeded: > 15% retry rate → channel too congested/interfered
→ Trigger re-evaluation → potentially switch channels
```

**Receive error threshold:**

```
FortiAP tracks: Packets received with errors (CRC errors, etc.)
Normal: < 1% error rate
Threshold exceeded: > 5% error rate → significant interference present
→ Trigger re-evaluation → potentially switch channels
```

> 💡 **Trainer Tip:** Phase 2 is what makes DARRP **reactive** between scheduled optimization cycles. If interference suddenly spikes and exceeds thresholds, DARRP can respond without waiting for the next 24-hour cycle.

---

---

# 📄 PAGE 210 — DARRP Profiles

---

## 🖥️ SLIDE SUMMARY

The slide explains **DARRP profiles** — customization of DARRP behavior:

**DARRP profile hierarchy:**

- **Default DARRP profile** — used automatically when DARRP is enabled on a radio
- **Custom DARRP profile** — overrides specific settings (timing, schedules, thresholds)
- Each radio on a FortiAP profile can use its own DARRP profile
- Multiple radios can **share** the same DARRP profile

**CLI Configuration:**

```bash
# Create custom DARRP profile
config wireless-controller arrp-profile
    edit "arrp-profile1"
        set override-darrp-optimize enable    # Override global timing
        set darrp-optimize 3600              # Run every 1 hour (instead of 24h)
        set darrp-optimize-schedules "always" # Run on this schedule
    next
end

# Apply DARRP profile to AP radio
config wireless-controller wtp-profile
    edit FAP231F-AP-PROFILE
        config radio-1
            set darrp enable
            set arrp-profile "arrp-profile1"  # Assign custom profile
        end
    end
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### When to Create Custom DARRP Profiles

**Scenario 1 — Business hours only optimization:**

```
DARRP profile with schedule: "weekdays-night"
  → DARRP runs only Monday-Friday, 2 AM - 4 AM
  → Avoids channel changes during business hours
  → Minimizes client disruption
```

**Scenario 2 — Dynamic environment (event venue):**

```
DARRP profile with darrp-optimize = 1800 (30 minutes)
  → Optimizes channels every 30 minutes
  → Keeps up with rapidly changing RF environment during events
```

**Scenario 3 — Stable environment:**

```
DARRP profile with darrp-optimize = 86400 (24 hours)
  → Default behavior — optimize once per day
  → Suitable for stable office environments
```

---

### DARRP Profile Inheritance — Flexibility

```
AP Profile "Floor-1-Profile":
  Radio 1 (2.4 GHz): DARRP profile = "arrp-profile-24h" (standard)
  Radio 2 (5 GHz):   DARRP profile = "arrp-profile-1h"  (aggressive)

AP Profile "Lobby-Profile":
  Radio 1 (2.4 GHz): DARRP profile = "arrp-profile-24h" (reused)
  Radio 2 (5 GHz):   DARRP profile = "arrp-profile-1h"  (reused)
```

Profiles are **reusable** — create once, apply to any radio across any AP profile.

---

---

# 📄 PAGE 211 — Auto Transmission Power

---

## 🖥️ SLIDE SUMMARY

The slide explains **three TX power modes**:

| Mode        | How It Works                                    | Use Case                  |
|-------------|-------------------------------------------------|---------------------------|
| **Percent** | Slider bar — percentage of region maximum       | Simple, visual adjustment |
| **dBm**     | Specific value between 1-33 dBm                 | Precise technical control |
| **Auto**    | FortiGate adjusts power within configured range | Dynamic adaptation        |

**Auto mode details:**

- Default range: **10 to 17 dBm**
- **Interference detected** → controller **reduces** TX power to `auto-power-low` threshold
- **No interference (or interference cleared)** → controller **increases** TX power to `auto-power-high` threshold

**dBm definition:** Electrical power unit referenced to **1 milliwatt**

---

## 📋 DETAILED INSTRUCTOR NOTES

### Understanding dBm — The Physics of TX Power

**dBm = decibels relative to 1 milliwatt**

```
Formula: dBm = 10 × log₁₀(Power in mW)

Conversions:
  0 dBm  = 1 mW
  3 dBm  = 2 mW   (every +3 dBm ≈ doubles power)
  10 dBm = 10 mW
  17 dBm = 50 mW
  20 dBm = 100 mW
  30 dBm = 1000 mW = 1 Watt (regulatory maximum in most regions)
```

**Practical impact of TX power on coverage:**

```
Higher TX power → longer range → larger coverage cell
Lower TX power → shorter range → smaller coverage cell

For dense deployments (many APs close together):
→ Lower TX power = smaller cells = less co-channel interference between APs
→ Counterintuitive: turning power DOWN improves performance in dense environments
```

> 💡 **Real-world analogy:** TX power is like the volume of a conversation. In a quiet room (sparse deployment), you speak loudly to be heard far away (high TX power). In a crowded party (dense deployment), everyone speaking loudly creates noise — you speak quietly and move close to the person you want to talk to (low TX power = smaller cells = better per-AP performance).

---

### Auto TX Power — Why It Matters

**The problem Auto mode solves:**

In a static TX power setup:

- High TX power → can reach far → but creates more co-channel interference with neighboring APs
- Low TX power → less interference → but may not cover all areas

**Auto TX power dynamically finds the balance:**

```
Ideal operation (no interference):
  TX power stays at auto-power-high (17 dBm)
  → Maximum coverage

Interference detected (neighboring AP on same channel):
  TX power reduced toward auto-power-low (10 dBm)
  → Smaller cell = less overlap with interfering AP
  → Less interference experienced by clients

Interference clears:
  TX power gradually increases back toward auto-power-high
  → Coverage expands again
```

---

### TX Power and Security

The slide mentions: *"You can decrease the power to prevent broadcasting signals to any unwanted area that may pose as a security risk."*

**Real security use case:**

```
Office on 5th floor of shared building
Reducing TX power prevents:
  → Signal bleeding to floors 4 and 6 (other tenants)
  → Competitors cannot scan your SSID from adjacent floors
  → Reduces attack surface from outside your physical space
```

This is a physical security control — complementing encryption and authentication.

---

---

# 📄 PAGE 212 — FortiAP Groups

---

## 🖥️ SLIDE SUMMARY

The slide introduces **FortiAP Groups** for simplified management:

**FortiAP Group characteristics:**

- Group APs **logically** (e.g., by floor, building, zone)
- All APs in a group can be assigned the **same profile simultaneously**
- Created on the **Group tab** in `WiFi & Switch Controller > Managed FortiAPs`

**Rules:**

- An AP can be in **only one group** at a time
- All APs in a group must be the **same model** (same platform)

---

## 📋 DETAILED INSTRUCTOR NOTES

### FortiAP Groups — Management Benefits

**Without groups (manual management at scale):**

```
100 APs across 5 floors (20 APs per floor)
Profile change needed → must manually update each AP or re-assign profile
→ 100 individual actions required
```

**With FortiAP groups:**

```
5 groups: Floor-1-Group, Floor-2-Group, ..., Floor-5-Group
Each group: 20 APs, all same model

Change profile for Floor-3-Group → all 20 APs updated simultaneously
→ 1 action instead of 20
```

---

### Same-Model Restriction — Why It Exists

FortiAP profiles are **model-specific** — a profile for FAP-231F has different radio settings than a profile for FAP-443K:

- Different number of radios
- Different supported bands
- Different antenna capabilities
- Different maximum TX power

If a group contained mixed models, you could not assign a single profile (the profile would be invalid for some models).

**Implication for deployment planning:**

- Standardize on one or two AP models per deployment area
- Don't mix models on the same floor or zone — it complicates group management
- If you must mix models, create separate groups per model and assign appropriate profiles

---

### FortiAP Groups vs. SSID Groups — Different Purposes

| Type               | Purpose                                   | Managed Where                     |
|--------------------|-------------------------------------------|-----------------------------------|
| **FortiAP Groups** | Group physical APs for profile assignment | `Managed FortiAPs > Group tab`    |
| **SSID Groups**    | Group SSIDs for profile inclusion         | `SSIDs > Create New > SSID Group` |

They complement each other:

- **SSID Group** defines WHAT is broadcast (which SSIDs)
- **FortiAP Group** defines WHERE it's broadcast (which APs) and HOW (which profile settings)

---

---

# 📊 Master Summary — Pages 197–212

```
┌──────────────────────────────────────────────────────────────────────────┐
│           FORTIAP PROFILES — COMPLETE GUIDE                              │
├─────────────────────────┬────────────────────────────────────────────────┤
│ TOPIC                   │ KEY FACTS                                      │
├─────────────────────────┼────────────────────────────────────────────────┤
│ AP Profile Basics       │ Defines: band, channels, TX power, SSIDs       │
│                         │ Model-specific — must match hardware           │
│                         │ CLI name: WTP profile                          │
│                         │ Default: [model]-default (e.g. FAP231F-default)│
├─────────────────────────┼────────────────────────────────────────────────┤
│ Authorization           │ AP unauthorized until admin approves           │
│                         │ Green icon = online, Gray = offline/pending    │
│                         │ Preauthorization: add serial before AP arrives │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Profile vs. Per-AP      │ Profile: same settings for all APs using it   │
│                         │ Per-AP override: CLI only, single AP exception │
│                         │ One AP = one profile (not multiple)            │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Custom Profile          │ Platform + Country MUST be set correctly       │
│                         │ Dual 5G (6E only) = no dedicated scan radio   │
│                         │ Dedicated scan = 3-radio APs only             │
│                         │ Direct access disabled by default (security)   │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Radio Settings          │ Mode: Disabled / Access Point / Dedicated Mon  │
│                         │ Bands: 2.4GHz / 5GHz / 6GHz (6E only)        │
│                         │ Width: 20/40/80/160 MHz                        │
│                         │ SGI: Off default — enable only if tested      │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Channel Selection       │ 2.4 GHz: Only channels 1, 6, 11 non-overlap   │
│                         │ 5 GHz: Up to 23 channels; DFS * = radar risk  │
│                         │ 6 GHz: New band, many channels, low congestion │
│                         │ DFS events: AP vacates channel within 200ms   │
├─────────────────────────┼────────────────────────────────────────────────┤
│ DARRP                   │ Auto channel selection per AP                  │
│                         │ Default: re-evaluates every 24 hours          │
│                         │ Phase 1: Calculate weights (noise, load)       │
│                         │ Phase 2: Monitor TX retries + RX errors        │
│                         │ ⚠️ Cannot detect intermittent interference     │
│                         │ Custom profiles: override timing + schedule    │
├─────────────────────────┼────────────────────────────────────────────────┤
│ TX Power                │ Percent / dBm (1-33) / Auto                   │
│                         │ Auto default: 10-17 dBm range                 │
│                         │ Interference → reduce power; clear → increase  │
│                         │ Lower TX = smaller cell = less interference    │
├─────────────────────────┼────────────────────────────────────────────────┤
│ FortiAP Groups          │ Group APs by floor/building/zone               │
│                         │ All must be same model (same platform)         │
│                         │ AP in one group only                           │
│                         │ Assign same profile to entire group at once    │
└─────────────────────────┴────────────────────────────────────────────────┘

⚠️  CRITICAL RULES TO REMEMBER:
  1. Always set correct Country/Region — regulatory violation risk if wrong
  2. Dual 5G mode = NO dedicated scan radio (trade-off decision)
  3. DFS channels → AP may vacate without warning → avoid near airports
  4. Per-AP overrides = CLI ONLY — document them carefully
  5. FortiAP groups = same model only — plan hardware standardization
  6. DARRP cannot detect intermittent interference — use dedicated scan radio
  7. Lower TX power in dense deployments → BETTER performance (not worse)
```

---

### Topic: FortiAP Profiles — Wireless Client Load Balancing

---

---

# 📄 PAGE 216 — Wireless Client Load Balancing Overview

---

## 🖥️ SLIDE SUMMARY

The slide introduces **wireless client load balancing** and its two methods:

**What it solves:**

- Distributes wireless traffic **more efficiently** among managed APs and available frequencies
- Designed specifically for **high-density deployments** (conference centers, auditoriums, open offices)

**Two load balancing types:**

| Method                | What It Does                          | Also Called                           |
|-----------------------|---------------------------------------|---------------------------------------|
| **AP Handoff**        | Moves clients between APs             | Client load balancing across FortiAPs |
| **Frequency Handoff** | Moves clients between frequency bands | Band steering across 2.4 / 5 / 6 GHz  |

**Key note from slide:**

- Configured **per AP profile**
- By default, load balancing is **NOT applied to roaming clients**

**GUI Path:** `WiFi & Switch Controller > AP Profiles`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Load Balancing Exists — The High-Density Problem

In a typical office with well-spaced APs, clients naturally distribute across APs based on signal strength. But in **high-density environments**, this breaks down:

```
Conference center scenario:
  500 people enter a room with 4 APs
  AP-1 (closest to main entrance): 280 clients
  AP-2 (back of room):              80 clients
  AP-3 (left side):                  90 clients
  AP-4 (right side):                 50 clients

Without load balancing:
  → AP-1 completely overwhelmed → terrible performance for 280 people
  → AP-2, 3, 4 underutilized → wasted capacity
  → 500 people all blame "bad Wi-Fi" when only AP-1 has the problem

With load balancing:
  → AP Handoff redistributes clients evenly → ~125 per AP
  → All clients get consistent, good performance
```

> 💡 **Real-world analogy:** Without load balancing, wireless is like a theme park where everyone rushes to the most popular ride and stands in a 3-hour line, while other identical rides nearby have no queue at all. Load balancing is like having ride attendants who direct people to shorter queues — same total capacity, dramatically better experience.

---

### Why Load Balancing Is NOT Applied to Roaming Clients by Default

**Roaming client** = a client that is actively moving between APs during a wireless session (e.g., a person walking with a phone in hand).

**Why exclude roaming clients from load balancing:**

- A roaming client has already made a deliberate choice to switch APs (based on signal)
- Applying load balancing to a roaming client could force it to connect to a farther, less optimal AP
- This would **increase latency, reduce throughput, and disrupt the roaming handoff**
- The roaming decision should be driven by **signal quality**, not by AP load

> ⚠️ **Trainer Note:** This is an important design distinction — load balancing targets **stationary clients** making initial connections, not clients actively moving through the coverage area.

---

### How APs Share Load Information

For load balancing to work, FortiAPs must share real-time load metrics with the wireless controller (FortiGate):

- Each AP reports its **current client count** to FortiGate via CAPWAP control channel
- FortiGate maintains a **real-time client distribution table** across all managed APs
- When a load-balancing decision is needed, FortiGate consults this table to find the **least busy nearby AP**

This is why FortiGate as a **centralized wireless controller** is essential for load balancing — without centralized visibility, no single AP knows how loaded its neighbors are.

---

---

# 📄 PAGE 217 — Load Balancing AP Handoff

---

## 🖥️ SLIDE SUMMARY

The slide explains **AP Handoff** — moving clients between APs:

**Two AP Handoff scenarios:**

| Scenario            | Trigger                             | Action                                                                 |
|---------------------|-------------------------------------|------------------------------------------------------------------------|
| **AP at capacity**  | Client count exceeds maximum        | Client with **lowest RSSI** (weakest signal) forced to join another AP |
| **AP at threshold** | Client count exceeds soft threshold | **New clients** redirected to least busy nearby AP                     |

**Signal requirement:**

- The receiving AP must see a signal strength **equal to or better than** the configured RSSI threshold
- Client's RSSI at the receiving AP must meet the minimum to qualify

**Configuration:**

- **Enable/disable:** GUI (AP profile)
- **Threshold values:** CLI only

---

## 📋 DETAILED INSTRUCTOR NOTES

### Two Distinct Scenarios — Critical Understanding

This is the most commonly confused aspect of AP Handoff. There are **two separate mechanisms** that trigger at different times:

---

#### Scenario 1 — AP Is Overloaded (Existing Clients)

```
AP-1 current clients: 35 (above threshold of 30)
Situation: AP-1 is already overloaded with existing clients

Action:
  FortiGate identifies the client on AP-1 with the LOWEST RSSI
  (weakest signal = likely furthest from AP or most marginal)
  FortiGate instructs AP-1 to disassociate that client
  Client reconnects → FortiGate steers to AP-2 (least busy nearby)
  Client must have acceptable RSSI at AP-2 (≥ handoff-rssi threshold)

Result: AP-1 load reduced by 1; AP-2 gains 1 client
```

**Why target the client with the lowest RSSI?**

- That client is the one with the **weakest connection** to AP-1
- It benefits most from moving to a potentially closer AP
- It's the "marginal" client — it could be served better elsewhere
- Moving a client with strong RSSI from AP-1 would degrade that client's experience unnecessarily

---

#### Scenario 2 — AP Is Busy, New Client Wants to Join

```
AP-1 current clients: 32 (above threshold of 30)
New client arrives at AP-1's coverage area and sends probe request

Action:
  AP-1 detects it is above threshold
  FortiGate instructs AP-1 to IGNORE the new client's probe
  FortiGate instructs the LEAST BUSY nearby AP (e.g., AP-3 with 18 clients) 
      to respond to the new client
  New client sees AP-3 respond and associates with AP-3
  
Result: New client joins AP-3; AP-1 stays at 32 (not 33)
```

> 💡 **Real-world analogy:** Scenario 1 is like a nightclub bouncer asking someone to leave because it's at capacity — and specifically asking the person standing by the back door (weakest connection). Scenario 2 is like the bouncer at a busy club not acknowledging a new guest's attempt to enter, but the door staff at the quieter club next door stepping forward to welcome them.

---

### 802.11 Association Response Status Code 17

The slide on the next page mentions this — let's introduce it here:

When an overloaded AP rejects a new client's association request, it sends an **802.11 Association Response frame with Status Code 17**:

```
Status Code 17 = "Association denied because the AP is unable to handle additional associated stations"
```

This is a **standard 802.11 mechanism** — the client receives this code and understands it should look for another AP. This is what triggers the client to try other APs until it finds one that responds with Success (Status Code 0).

---

---

# 📄 PAGE 218 — Load Balancing AP Handoff (Continued)

---

## 🖥️ SLIDE SUMMARY

The slide provides the **visual diagram and workflow** for AP Handoff:

```
                    AP2
                   (less busy)
                    ↑
                    | Client with lowest RSSI steered here
                    |
AP1 threshold    AP1
reached ──────→  (overloaded: >30 clients)
```

**Key workflow:**

1. AP-1 exceeds threshold (e.g., >30 clients)
2. New client tries to join AP-1
3. Wireless controller selects **least busy nearby AP** (AP-2)
4. AP-2 responds to the new client's join request
5. Client joins AP-2

**Technical mechanism:**

- AP-1 sends **802.11 Association Response with Status Code 17** (AP busy)
- New client retries → AP-2 responds → client associates with AP-2

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Status Code 17 Mechanism — How Clients Experience It

From the **client's perspective**:

```
1. Client sends 802.11 Probe Request
2. AP-1 responds with Probe Response (AP-1 is seen in the network list)
3. Client sends 802.11 Authentication Request to AP-1
4. AP-1 sends Authentication Response → Success (allows auth phase)
5. Client sends 802.11 Association Request to AP-1
6. AP-1 sends Association Response → STATUS CODE 17 (AP BUSY)
7. Client knows this AP is full → tries again or tries other APs
8. AP-2 has been instructed by controller to respond → sends Status 0 (Success)
9. Client associates with AP-2
```

**Why the client is allowed to authenticate but denied association:**

- 802.11 has two separate phases: Authentication and Association
- The AP allows the Authentication phase to complete (no load information at that point)
- The LOAD CHECK happens at the Association phase — this is where Status Code 17 is returned

> 💡 **Trainer Tip:** This sequence is important for troubleshooting. If you see Status Code 17 in packet captures, it's expected behavior from AP Handoff — not an error. Students often mistake it for an authentication failure.

---

### Why "Least Busy Nearby AP"?

Two words are critical: **least busy** AND **nearby**:

**Least busy:**

- FortiGate always redirects to the AP with the fewest current clients
- Ensures even distribution across all available APs in the area

**Nearby:**

- The client must have an acceptable RSSI at the receiving AP
- Sending a client to an AP on the other side of the building would result in poor performance
- The RSSI threshold (`handoff-rssi`) ensures the client only moves to an AP where it can get a good signal

```
Client at AP-1 (overloaded):
  AP-2 nearby: 15 clients, RSSI -68 dBm ← qualifies (strong signal)
  AP-3 nearby: 10 clients, RSSI -80 dBm ← too weak (below threshold)
  AP-4 far away: 5 clients, RSSI -85 dBm ← too weak (below threshold)

→ FortiGate chooses AP-2 (least busy that meets RSSI requirement)
```

---

---

# 📄 PAGE 219 — Configuring AP Handoff

---

## 🖥️ SLIDE SUMMARY

The slide shows the **CLI configuration** for AP Handoff:

```bash
config wireless-controller wtp-profile
    edit <profile name>
        set handoff-sta-thresh 30    # Clients before AP handoff initiates
        set handoff-rssi 25          # Minimum RSSI threshold
        set ap-handoff enable        # MUST enable to activate
        set max-client 0             # 0 = unlimited (no hard cap)
    end
```

**Parameter details:**

| Parameter            | Range          | Default      | Description                              |
|----------------------|----------------|--------------|------------------------------------------|
| `handoff-sta-thresh` | 5 – 35         | —            | Client count that triggers AP handoff    |
| `handoff-rssi`       | 20 – 30        | 25           | Minimum signal strength for receiving AP |
| `ap-handoff`         | enable/disable | **disabled** | Master switch for AP Handoff             |
| `max-client`         | 0 = unlimited  | 0            | Hard maximum — hard rejection above this |

**Best practice thresholds:**

- General use: **handoff-sta-thresh = 15 to 20**
- Video streaming: **up to 10 stations** (much more conservative)

---

## 📋 DETAILED INSTRUCTOR NOTES

### max-client vs. handoff-sta-thresh — Critical Distinction

This is the most important conceptual distinction on this page and a very common exam/interview question:

```
handoff-sta-thresh = SOFT threshold (graceful steering)
  → When reached: new clients redirected to other APs
  → Existing clients may be steered away (weakest RSSI first)
  → Clients CAN still join IF no other AP is available
  → Soft, cooperative, performance-focused

max-client = HARD limit (forced rejection)
  → When reached: ALL new connections HARD rejected
  → No exceptions — client CANNOT join regardless of circumstances
  → AP-1 at max-client = 30 → client 31 gets rejected entirely
  → Client must find another AP or has no wireless access
  → max-client = 0 means NO hard limit
```

> 💡 **Real-world analogy:** `handoff-sta-thresh` is like a restaurant that starts recommending the next-door café to new customers when it gets busy — you COULD still get a table if you insist, but they steer you elsewhere. `max-client` is like a fire marshal with a strict 30-person maximum — when the room is full, the door is physically locked. No exceptions.

---

### RSSI Value Interpretation — The dB Math

The slide states:

> *"A value of 25 means that the signal level needs to be -70 dBm or better, and a value of 30 means the signal threshold is -65 dBm."*

**Understanding the conversion:**

```
handoff-rssi value = Signal level relative to noise floor (-95 dBm)

handoff-rssi = 25:
  -95 dBm (noise floor) + 25 dB = -70 dBm minimum RSSI at receiving AP

handoff-rssi = 30:
  -95 dBm (noise floor) + 30 dB = -65 dBm minimum RSSI at receiving AP

handoff-rssi = 20 (minimum allowed):
  -95 dBm + 20 dB = -75 dBm minimum RSSI
```

**Signal quality reference:**

```
RSSI Range     Quality          Connection Quality
─────────────────────────────────────────────────
-50 dBm+       Excellent        Full throughput
-60 dBm        Very Good        High throughput
-70 dBm        Good             Reliable, adequate throughput
-75 dBm        Fair             Usable but reduced speed
-80 dBm        Poor             Unstable, frequent drops
-85 dBm+       Very Poor        Nearly unusable
```

**Setting handoff-rssi = 25 (-70 dBm) means:**

- A client will only be steered to AP-2 if AP-2 sees that client at -70 dBm or better
- Ensures the client gets a **"Good" or better** connection at the new AP
- Prevents steering clients to APs where they'll have poor performance

---

### Best Practice Values — Why Video Streaming Gets Lower Thresholds

**For general data: handoff-sta-thresh = 15-20**

```
Why 15-20 and not 30?
  At 30 clients per radio, per-client throughput is already degrading
  Starting handoff at 15-20 keeps performance high throughout
  Proactive steering before degradation is noticeable
```

**For video streaming: handoff-sta-thresh ≤ 10**

```
Why so conservative for video?
  Video streaming requires SUSTAINED, CONSISTENT throughput
  Each additional client on a radio steals airtime from all others
  A brief throughput drop causes video buffering/freezing
  At 10 clients on a video-heavy AP → consistent high throughput for all
  Users notice video quality much more than data latency
```

> ⚠️ **Trainer Warning:** `ap-handoff` is **disabled by default**. Many admins configure `handoff-sta-thresh` and `handoff-rssi` via CLI and then wonder why load balancing isn't working — they forgot to `set ap-handoff enable`. Always verify the master switch is on after configuring thresholds.

---

### Must Enable on ALL Radios

The slide notes: *"You must enable the AP-handoff feature on all radios on an AP."*

```bash
config wireless-controller wtp-profile
    edit <profile name>
        set ap-handoff enable        # Enable on profile level
        config radio-1
            set ap-handoff enable    # ALSO enable per radio
        end
        config radio-2
            set ap-handoff enable    # ALSO enable per radio
        end
    end
```

If you enable AP Handoff on the profile but not on individual radios, load balancing will not function correctly.

---

---

# 📄 PAGE 220 — Frequency Handoff — AP Band Steering

---

## 🖥️ SLIDE SUMMARY

The slide explains **Frequency Handoff (Band Steering)** — moving clients between frequency bands:

```
Client with dual-band capability:
  Tries to join 2.4 GHz SSID
          ↓
  Wireless controller evaluates:
    ✅ Client is dual-band capable
    ✅ RSSI is strong on 5 GHz
          ↓
  Controller does NOT reply to 2.4 GHz probe
  Client times out → retries → now tries 5 GHz
          ↓
  Controller responds on 5 GHz → client joins 5 GHz
```

**The controller's decision process:**

1. Checks if client is **dual-band capable**
2. Checks if **RSSI is strong** on 5 GHz
3. If both true → ignores 2.4 GHz request → forces retry on 5 GHz

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Band Steering Problem — Why 2.4 GHz Gets Overcrowded

**Why clients prefer 2.4 GHz by default:**

- Older devices only support 2.4 GHz → trained legacy behavior
- 2.4 GHz has longer range → device's algorithm defaults to the band it "sees" most strongly
- Many device drivers still prefer 2.4 GHz unless actively steered

**The cascade effect without band steering:**

```
100 dual-band capable clients in a conference room:
  80 clients connect to 2.4 GHz (3 non-overlapping channels → severely congested)
  20 clients connect to 5 GHz (23 channels → barely used)

Result:
  2.4 GHz: 80 clients fighting over 3 channels → terrible performance
  5 GHz: 20 clients with 23 channels to themselves → excellent performance
  → 80% of clients have poor experience when they could have excellent experience
```

**With frequency handoff enabled:**

```
100 dual-band capable clients arrive:
  FortiGate steers dual-band clients to 5 GHz
  Only 2.4 GHz-only devices remain on 2.4 GHz

Result:
  2.4 GHz: 10 legacy/single-band clients → much less congestion
  5 GHz: 90 dual-band clients → still plenty of capacity
  → Everyone gets dramatically better performance
```

---

### The Mechanics of Band Steering — Step by Step

```
Step 1: Client sends Probe Request on 2.4 GHz
         FortiGate records: "Client MAC aa:bb:cc → requesting 2.4 GHz"

Step 2: FortiGate checks client capability table
         "Has this MAC appeared on 5 GHz before?" → checks probe history
         OR
         "Did the probe request indicate dual-band support?" → capability bits

Step 3: FortiGate checks RSSI at 5 GHz radio
         "What signal strength do we see from this client on 5 GHz?"
         RSSI on 5 GHz = -65 dBm (strong) → qualifies for band steering

Step 4: FortiGate ignores the 2.4 GHz probe
         No Association Response sent on 2.4 GHz
         Client waits... timeout occurs

Step 5: Client firmware retries on 5 GHz
         Sends Probe Request on 5 GHz
         FortiGate responds → client associates on 5 GHz

Step 6: If RSSI on 5 GHz is NOT acceptable:
         FortiGate accepts the client on 2.4 GHz on second attempt
         → Never permanently lock out a client from 2.4 GHz
```

> 💡 **Real-world analogy:** Band steering is like a hotel concierge who offers a guest an upgraded room (5 GHz) instead of their requested standard room (2.4 GHz). If the guest really needs the standard room (weak 5 GHz signal), the concierge accommodates them. If the guest qualifies for the upgrade, the concierge temporarily "loses" the standard room reservation until the guest asks about the upgrade.

---

### Why the Controller Ignores (Not Rejects) the 2.4 GHz Request

The mechanism is **silence** — not an active rejection:

- The AP simply does **not respond** to the 2.4 GHz probe
- There is no "rejection frame" sent — just no response
- The client's 802.11 stack interprets silence as "AP not responding" → tries other options
- Eventually the client tries 5 GHz → gets a response → connects

**Why not send a rejection?**

- There is no standard 802.11 mechanism to say "go to 5 GHz instead"
- Silence is the only standards-compliant way to steer clients
- Using proprietary rejection codes would break non-Fortinet clients

---

---

# 📄 PAGE 221 — Load Balancing Frequency Handoff

---

## 🖥️ SLIDE SUMMARY

The slide details the **client capability tracking mechanism** for frequency handoff:

**How FortiGate tracks client band capability:**

- Uses **probe responses** to determine if clients support 5 GHz or 6 GHz
- Maintains a **table per client MAC address** containing:
  - Bands supported (2.4/5/6 GHz)
  - RSSI value on each band

**Conditions for 5 GHz/6 GHz steering:**

| Condition             | Requirement                                                |
|-----------------------|------------------------------------------------------------|
| **Dual-band support** | Client must support 5 GHz (or 6 GHz for Wi-Fi 6E)          |
| **RSSI strength**     | Signal on 5 GHz/6 GHz must meet the handoff-rssi threshold |

**Fallback behavior:**

- If EITHER condition is not met → FortiGate **accepts client on 2.4 GHz**
- FortiGate **never permanently locks** a client out of 2.4 GHz

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Client Capability Table — How FortiGate Learns

FortiGate builds a **per-client MAC address table** by listening to 802.11 management frames:

**Information sources:**

```
Probe Requests:
  → Contain "Supported Rates" → reveals max data rate → indicates band capability
  → Appear on whichever band the client is searching

Association Requests:
  → Contain "HT Capabilities" (802.11n) → confirms 5 GHz support
  → Contain "VHT Capabilities" (802.11ac) → confirms 5 GHz high-throughput
  → Contain "HE Capabilities" (802.11ax/Wi-Fi 6) → confirms Wi-Fi 6 support

Historical data:
  → Has this MAC ever probed on 5 GHz? → dual-band capable
  → Has this MAC ever associated on 5 GHz before? → definitely dual-band
```

**The table entry for each client:**

```
MAC: aa:bb:cc:dd:ee:ff
  Band 2.4 GHz: Supported → RSSI: -62 dBm
  Band 5 GHz:  Supported → RSSI: -67 dBm
  Band 6 GHz:  Not supported
  Verdict: Steer to 5 GHz (supported + RSSI acceptable)
  
MAC: ff:ee:dd:cc:bb:aa
  Band 2.4 GHz: Supported → RSSI: -70 dBm
  Band 5 GHz:  Not detected (older device)
  Band 6 GHz:  Not supported
  Verdict: Allow 2.4 GHz (single-band device)
```

---

### Benefits for ALL Clients — Including 2.4 GHz-Only Devices

The slide emphasizes this important point:

> *"Clients that do not support the faster frequency bands benefit because there is less interference on 2.4 GHz because of the reduced number of clients."*

This is the **secondary benefit** of frequency handoff — often overlooked:

```
Without band steering:
  2.4 GHz: 80 clients (dual-band + legacy) → severe congestion
  Legacy devices get terrible performance → competing with 80 clients

With band steering:
  2.4 GHz: 15 clients (legacy only) → much less congestion
  Legacy devices get good performance → only competing with 15 clients
```

Dual-band clients move to 5 GHz → their absence improves 2.4 GHz for those who truly need it.

---

### 6 GHz Steering with Wi-Fi 6E FortiAPs

On Wi-Fi 6E FortiAPs with a 6 GHz radio:

- FortiGate extends the steering hierarchy: **2.4 GHz → 5 GHz → 6 GHz**
- Clients with Wi-Fi 6E capability (tri-band) are steered to 6 GHz if RSSI is sufficient
- 6 GHz provides: massive spectrum, minimal congestion, highest throughput
- Only Wi-Fi 6E-capable devices can use 6 GHz → acts as a natural high-performance tier

```
Client tier assignment:
  Wi-Fi 6E capable + strong 6 GHz RSSI → 6 GHz (best performance)
  Wi-Fi 6 capable + strong 5 GHz RSSI → 5 GHz (good performance)
  2.4 GHz only → 2.4 GHz (acceptable for legacy devices)
```

---

---

# 📄 PAGE 222 — Frequency Handoff Configuration

---

## 🖥️ SLIDE SUMMARY

The slide shows the **GUI configuration** for Frequency Handoff in the AP profile:

**Configuration location:** `WiFi & Switch Controller > FortiAP Profile` → Client Load Balancing section

**Settings:**

- Enable/disable **Frequency Handoff** (band steering)
- Configure **handoff-rssi** threshold value

**Critical requirement:**

- Must enable frequency handoff on **5 GHz or 6 GHz band** to learn client capability

**Best practice RSSI thresholds:**

| Traffic Type               | Recommended handoff-rssi | Equivalent RSSI     |
|----------------------------|--------------------------|---------------------|
| **Voice traffic**          | 25 – 30                  | \-70 dBm to -65 dBm |
| **Regular data and video** | 23 – 27                  | \-72 dBm to -68 dBm |

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Enable Frequency Handoff on 5 GHz Radio?

This is a subtle but **critical configuration requirement**:

```
Frequency handoff must be ENABLED on the 5 GHz radio

Why?
  The 5 GHz radio needs to actively monitor which clients it can HEAR
  If 5 GHz radio doesn't know about a client:
    → FortiGate cannot measure RSSI for that client on 5 GHz
    → FortiGate doesn't know if the client is close enough to steer
    → FortiGate cannot make the band steering decision
    
  With frequency handoff enabled on 5 GHz:
    → 5 GHz radio passively listens for probe requests from all clients
    → Builds the RSSI database for every client it can hear
    → FortiGate can make informed band steering decisions
```

> ⚠️ **Trainer Warning:** A very common mistake — enabling Frequency Handoff on the AP profile but forgetting to ensure it's enabled on the 5 GHz radio settings. Band steering will not work without the 5 GHz radio actively listening for client capabilities.

---

### RSSI Threshold Configuration — Voice vs. Data

**Why voice traffic needs a HIGHER RSSI threshold (25-30):**

```
Voice traffic characteristics:
  Extremely sensitive to PACKET LOSS and JITTER
  Even a 1% packet loss → audible degradation (choppy audio)
  Even 50ms jitter → noticeable audio problems
  
Implication for band steering:
  If voice client is steered to 5 GHz with marginal signal (-75 dBm):
    → Occasional packet loss during brief signal drops
    → MOS (Mean Opinion Score) drops below acceptable
    → User hears choppy/cutting audio → worse than staying on 2.4 GHz
    
  Solution: Only steer voice clients to 5 GHz if signal is STRONG (-70 dBm or better)
  → Higher threshold = more conservative steering = fewer steering events
  → Voice quality preserved
```

**Why data/video traffic can use a LOWER threshold (23-27):**

```
Data and video characteristics:
  Data: TCP retransmission handles occasional packet loss
  Video: Brief buffering handles momentary quality reduction
  More tolerant of slight signal variability
  
Implication:
  Can steer to 5 GHz with somewhat weaker signal (-72 dBm)
  → Still beneficial to be on 5 GHz vs. crowded 2.4 GHz
  → Slight occasional quality drop acceptable vs. constant 2.4 GHz congestion
```

---

### GUI vs. CLI Configuration for Load Balancing

| Setting                               | Available in GUI  | Available in CLI |
|---------------------------------------|-------------------|------------------|
| Enable/disable AP Handoff             | ✅ Yes             | ✅ Yes            |
| Enable/disable Frequency Handoff      | ✅ Yes             | ✅ Yes            |
| **handoff-sta-thresh** (client count) | ❌ **CLI only**    | ✅ Yes            |
| **handoff-rssi** (signal threshold)   | ✅ GUI (frequency) | ✅ Yes            |
| **max-client** (hard limit)           | ✅ Yes             | ✅ Yes            |

> ⚠️ **Trainer Warning:** The most critical parameter — `handoff-sta-thresh` — is **CLI only**. Many admins enable AP Handoff in the GUI but never configure the threshold, leaving it at default. Always verify via CLI after enabling load balancing in the GUI.

---

### Complete CLI Configuration for Both Load Balancing Types

```bash
config wireless-controller wtp-profile
    edit "Dense-Environment-Profile"
        
        # AP Handoff Settings
        set ap-handoff enable
        set handoff-sta-thresh 20          # Start steering at 20 clients
        set handoff-rssi 25                # Target AP must see client at -70dBm+
        set max-client 0                   # No hard limit (let handoff manage)
        
        # Radio 1 (2.4 GHz) settings
        config radio-1
            set band 802.11ax-2G
            set frequency-handoff enable   # Enable band steering on 2.4GHz radio
            set ap-handoff enable
        end
        
        # Radio 2 (5 GHz) settings  
        config radio-2
            set band 802.11ax-5G
            set frequency-handoff enable   # REQUIRED on 5GHz to learn clients
            set ap-handoff enable
        end
        
    end
end
```

---

### Interaction Between AP Handoff and Frequency Handoff

Both can be enabled simultaneously — they work at **different levels**:

```
Frequency Handoff (Band Steering):
  SAME AP, different radio
  Client moves from 2.4 GHz → 5 GHz on the SAME physical AP
  No change in AP association
  
AP Handoff:
  DIFFERENT AP, possibly same or different band
  Client moves from AP-1 → AP-2
  Association changes to a different physical AP
```

**Priority order when both are enabled:**

```
1. FortiGate first evaluates: Should this client be on 5 GHz?
   → Frequency Handoff evaluation
   
2. Then evaluates: Is this AP overloaded?
   → AP Handoff evaluation
   
3. If both triggered → Frequency Handoff takes priority
   (Better to fix the band first before potentially moving APs)
```

---

---

# 📊 Master Summary — Pages 216–222

```
┌──────────────────────────────────────────────────────────────────────────┐
│         WIRELESS CLIENT LOAD BALANCING — COMPLETE GUIDE                  │
├──────────────────────────┬───────────────────────────────────────────────┤
│ FEATURE                  │ KEY FACTS                                     │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Load Balancing           │ Two types: AP Handoff + Frequency Handoff     │
│ Overview                 │ Designed for HIGH-DENSITY deployments         │
│                          │ Disabled by default — must explicitly enable  │
│                          │ NOT applied to roaming clients by default     │
│                          │ Configured per AP profile                     │
├──────────────────────────┼───────────────────────────────────────────────┤
│ AP Handoff               │ Moves clients BETWEEN APs (different hardware)│
│ (Client Load Balancing)  │ Trigger 1: AP at capacity → evict lowest RSSI│
│                          │ Trigger 2: AP at threshold → redirect new     │
│                          │           clients to least busy nearby AP     │
│                          │ Status Code 17 = AP BUSY signal to client     │
│                          │ Must enable on profile AND each radio         │
├──────────────────────────┼───────────────────────────────────────────────┤
│ AP Handoff               │ handoff-sta-thresh: 5-35 (best: 15-20)       │
│ CLI Parameters           │ handoff-rssi: 20-30 (default: 25 = -70dBm)  │
│                          │ ap-handoff: enable/disable (default: DISABLED)│
│                          │ max-client: hard limit (0 = unlimited)        │
│                          │ ⚠️ handoff-sta-thresh = CLI ONLY             │
├──────────────────────────┼───────────────────────────────────────────────┤
│ max-client vs            │ handoff-sta-thresh = SOFT threshold           │
│ handoff-sta-thresh       │   → Graceful steering, clients can still join │
│                          │ max-client = HARD limit                       │
│                          │   → Absolute rejection, no exceptions         │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Frequency Handoff        │ Moves clients BETWEEN BANDS (same AP)         │
│ (Band Steering)          │ Steers dual-band clients from 2.4→5 or 6GHz  │
│                          │ Uses SILENCE (ignores probe) to force retry   │
│                          │ Two conditions: dual-band capable + RSSI OK   │
│                          │ Never permanently locks client out of 2.4 GHz │
│                          │ Also benefits 2.4-only devices (less crowd)   │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Frequency Handoff        │ Must enable on 5GHz radio to learn clients    │
│ Configuration            │ Voice RSSI: 25-30 (-70 to -65 dBm)          │
│                          │ Data/Video RSSI: 23-27 (-72 to -68 dBm)     │
├──────────────────────────┼───────────────────────────────────────────────┤
│ RSSI Conversion          │ handoff-rssi value + (-95 dBm) = threshold   │
│                          │ 25 → -70 dBm | 30 → -65 dBm | 20 → -75 dBm │
└──────────────────────────┴───────────────────────────────────────────────┘

⚠️  CRITICAL RULES TO REMEMBER:
  1. BOTH load balancing types are DISABLED by default
  2. handoff-sta-thresh is CLI ONLY — always verify after GUI config
  3. AP Handoff must be enabled on profile AND each radio individually
  4. Frequency Handoff REQUIRES it enabled on the 5GHz radio to work
  5. max-client (hard) ≠ handoff-sta-thresh (soft) — never confuse them
  6. Band steering works by SILENCE — not active rejection
  7. Load balancing is NOT applied to roaming clients by default
  8. Video: use conservative threshold (≤10 clients) to protect QoS
```

---

### Topic: FortiAP Profiles — WIDS (Wireless Intrusion Detection System)

---

---

# 📄 PAGE 226 — WIDS Overview

---

## 🖥️ SLIDE SUMMARY

The slide introduces **WIDS (Wireless Intrusion Detection System)**:

**What WIDS does:**

- Monitors wireless traffic for **Wi-Fi-specific security threats**
- Detects and **reports** on possible intrusion attempts
- When an attack is detected → FortiOS records a **log message**

**WIDS profile:**

- A set of **intrusion detection settings** including rogue AP detection
- Must be **applied to an AP profile** to start detecting and reporting

**WIDS detects these threats:**

| Threat                          | Description                                        |
|---------------------------------|----------------------------------------------------|
| 🔓 **Weak WEP encryption**      | Detects weak WEP IVs used to crack WEP keys        |
| 📡 **Null SSID probe response** | Causes wireless cards to stop responding           |
| 🚫 **Spoofed deauthentication** | DoS attack — forces all clients to disconnect      |
| 🔢 **Invalid MAC OUI**          | Detects randomly generated/spoofed MAC addresses   |
| 🌊 **Various floods**           | Management, EAP, authentication, and beacon floods |

---

## 📋 DETAILED INSTRUCTOR NOTES

### What Is WIDS and Why It Matters

**WIDS (Wireless Intrusion Detection System)** is FortiOS's built-in capability to **monitor the wireless medium** for attacks and unauthorized activity — beyond just serving clients.

Without WIDS:

- FortiAP serves clients and manages connections
- Has **no visibility** into hostile wireless activity around it
- An attacker could launch wireless attacks undetected

With WIDS enabled:

- FortiAP continuously monitors wireless frames **in addition to serving clients**
- Detects attack signatures, anomalous behavior, and unauthorized devices
- Reports to FortiGate → logs generated → FortiAnalyzer can trigger alerts and automated responses

> 💡 **Real-world analogy:** Without WIDS, FortiAP is like a security guard who only checks badges at the front door — they don't notice suspicious activity in the parking lot. With WIDS, the security guard also monitors CCTV covering the perimeter — threats are detected before they even reach the door.

---

### WIDS vs. Traditional IDS/IPS — Key Difference

**Traditional IDS/IPS (FortiGate UTM):**

- Inspects **network layer traffic** — Layer 3 and above (IP packets, TCP sessions)
- Detects: application exploits, malware, policy violations
- Works on wired/VPN/internet-facing traffic

**WIDS:**

- Inspects **wireless management frames** — Layer 2 (802.11 frames)
- Detects: wireless-specific attacks, rogue APs, 802.11 protocol abuse
- Works on **RF medium** — can detect threats that never even reach the wired network

These two systems are **complementary** — neither replaces the other:

```
Attack enters wireless network:
  WIDS layer: Detects wireless-level attack (deauth flood, rogue AP)
  FortiGate UTM layer: Inspects IP-level traffic from wireless clients
  Both layers working together = comprehensive wireless security
```

---

### The WIDS Profile — Configuration Container

A **WIDS profile** is a named configuration object that:

- Defines **which attacks to detect**
- Sets **thresholds** for each attack type (e.g., how many frames = flood)
- Is applied to one or more **AP profiles**
- Can be created multiple times for different security requirements

```
WIDS Profile "High-Security":
  → All attack types enabled
  → Low thresholds (very sensitive)
  → Applied to executive floor APs

WIDS Profile "Standard":
  → Common attacks enabled
  → Default thresholds
  → Applied to general office APs

WIDS Profile "Guest-Area":
  → Basic detection only
  → Higher thresholds (less sensitive)
  → Applied to lobby/guest APs
```

> ⚠️ **Trainer Warning:** A WIDS profile does **nothing** until it is applied to an AP profile. Creating the profile is only half the job — always verify the profile is correctly linked to the AP profile to activate monitoring.

---

### Wireless-Specific Attacks — Why They're Unique

The attacks WIDS detects are **unique to the wireless medium**:

**Why can't FortiGate UTM detect these?**

- These attacks operate at **Layer 2 (802.11 frames)** — before packets reach the IP layer
- FortiGate receives only **processed traffic** from the CAPWAP tunnel — not raw 802.11 frames
- Only the FortiAP, operating directly on the RF medium, can see and analyze raw 802.11 management frames
- WIDS detection must happen **at the AP level** — FortiGate analyzes the results, not the raw frames

---

---

# 📄 PAGE 227 — Provisioning Rogue AP Detection (Background Scan)

---

## 🖥️ SLIDE SUMMARY

The slide shows the **WIDS profile configuration for background rogue AP detection**:

**Key settings for background scanning:**

- **Scan interval:** Radio switches to monitor mode every **600 seconds** (default) to perform background scan
- **Probe requests:** AP does **NOT** send probe requests on scanned channels (passive listening only)

**GUI Path:** `WiFi & Switch Controller > WIDS Profiles`

**What this enables:**

- **Background rogue AP scanning** — periodic scanning while still serving clients
- FortiAP briefly pauses client service → scans all channels → returns to normal service
- Detects unknown/unauthorized APs broadcasting in the area

---

## 📋 DETAILED INSTRUCTOR NOTES

### Background Scan vs. Foreground Scan — The Fundamental Difference

This is one of the most important conceptual distinctions in wireless security:

| Feature                | Background Scan                              | Foreground Scan (Dedicated Monitor) |
|------------------------|----------------------------------------------|-------------------------------------|
| **Radio mode**         | Normal Access Point                          | Dedicated Monitor                   |
| **Client service**     | ✅ Still serves clients                       | ❌ No client service                 |
| **Scanning**           | Periodic (interrupts client service briefly) | Continuous — all channels           |
| **Detection accuracy** | Lower — misses activity between scans        | Highest — continuous monitoring     |
| **Coverage**           | Limited — scans own channels primarily       | All channels rotated continuously   |
| **Hardware required**  | Any FortiAP                                  | Recommended: 3-radio AP             |

---

### How Background Scanning Works — Technical Mechanics

```
FortiAP Radio in Access Point mode (normal operation):

t=0:     Serving clients on Channel 6 (2.4 GHz)
t=600:   SWITCH → Monitor mode briefly
         Scan Channel 1 → Record all BSSIDs heard
         Scan Channel 6 → Record all BSSIDs heard
         Scan Channel 11 → Record all BSSIDs heard
         SWITCH BACK → Access Point mode
t=600+x: Serving clients on Channel 6 (resumes)

Report:
  Observed BSSIDs sent to FortiGate via CAPWAP control channel
  FortiGate compares against known authorized AP list
  Unknown BSSIDs flagged as potential rogue APs
```

**The 600-second interval:**

- Default: **600 seconds (10 minutes)**
- Configurable: shorter for more frequent scanning, longer for less service interruption
- Trade-off: More frequent scanning = more detection accuracy = more service interruptions

> 💡 **Real-world analogy:** Background scanning is like a store security guard who primarily watches shoppers but periodically walks the perimeter to check for intruders. They're not watching the perimeter constantly, but they check it regularly.

---

### Why APs Don't Send Probe Requests During Background Scan

The slide states: *"AP will not send probe requests on scanned channels."*

**Why passive scanning only?**

**Active scanning (with probe requests):**

- AP sends probe requests → neighboring APs and clients respond
- Faster discovery of all devices
- BUT: probe requests are **visible to attackers** — they know you're scanning
- Probe requests also **consume airtime** on client-serving channels

**Passive scanning (listen only):**

- AP simply listens for beacon frames and probe responses from other devices
- No transmissions from the FortiAP during the scan
- **Stealthy** — attackers don't know they're being monitored
- Less airtime consumption
- Sufficient for rogue AP detection (APs beacon continuously anyway)

---

---

# 📄 PAGE 228 — Provisioning On-Wire Rogue AP Detection

---

## 🖥️ SLIDE SUMMARY

The slide explains **on-wire rogue AP detection** — a more powerful detection and suppression method:

**Two suppression methods:**

- 👤 **Manual** — admin logs in, marks AP as rogue, enables suppression (slow)
- 🤖 **Automatic** — WIDS profile triggers automatic suppression (fast)

**Four required settings for automatic on-wire rogue AP suppression:**

| Step | Setting                                               | Where        |
|------|-------------------------------------------------------|--------------|
| 1    | AP/radio in **dedicated monitor mode**                | AP profile   |
| 2    | Enable **Sensor mode**                                | WIDS profile |
| 3    | Enable **Rogue AP detection**                         | WIDS profile |
| 4    | Enable **Auto-suppress rogue APs in foreground scan** | WIDS profile |

**GUI Path:** `WiFi & Switch Controller > WIDS Profiles`

> ⚠️ **Important:** Even for MANUAL suppression to work — steps 1, 2, and 3 must be enabled first.

---

## 📋 DETAILED INSTRUCTOR NOTES

### What Is a Rogue AP?

A **rogue AP** is any unauthorized wireless access point operating within or near your network:

**Types of rogue APs:**

| Type                          | Description                                | Threat Level                          |
|-------------------------------|--------------------------------------------|---------------------------------------|
| **Accidental rogue**          | Employee plugs in personal AP at office    | Medium — usually unintentional        |
| **Misconfigured AP**          | Company AP incorrectly configured          | Low — usually fixable                 |
| **Evil twin AP**              | Attacker AP mimicking your legitimate SSID | 🔴 **Critical** — credential theft    |
| **Rogue AP on wired network** | AP plugged into your switch by attacker    | 🔴 **Critical** — full network access |

---

### On-Wire Detection — The Critical Distinction

**Standard rogue AP detection (background scan):**

- Detects APs broadcasting **in the wireless space nearby**
- Cannot determine if the rogue AP is **connected to YOUR wired network**
- A rogue AP in a nearby building looks the same as one plugged into your switch

**On-wire rogue AP detection:**

- Determines if a detected wireless AP is actually **connected to your own wired network**
- How: FortiAP in monitor mode listens to wireless client traffic
- When a client associates with an unknown AP → FortiAP sees that client's traffic on the wireless medium
- FortiGate **checks if that client's MAC/IP appears on the wired network** (via ARP/DHCP tables)
- If the unknown AP's clients appear on your wired network → the rogue AP **is on your wire**

> �� **Real-world analogy:** Basic rogue detection is like spotting an unknown person standing outside your building. On-wire detection is like discovering that unknown person is using your internal elevator — they're not just outside, they're inside.

---

### The Four Required Settings — Deep Dive

**Step 1 — Dedicated Monitor Mode**

```
AP Profile → Radio configuration → Mode: Dedicated Monitor

Why required:
  Foreground scanning (continuous, all channels) requires FULL radio attention
  An Access Point radio serving clients cannot continuously monitor all channels
  Dedicated Monitor = radio gives 100% of its time to scanning
  → Can capture ALL wireless frames including client associations with rogue APs
  → Can send suppression frames (deauthentication) to rogue clients
```

**Step 2 — Sensor Mode in WIDS Profile**

```
WIDS Profile → Enable: Sensor Mode

What Sensor Mode does:
  Enables the FortiAP to listen to ALL wireless traffic
  Including: traffic between clients and UNKNOWN APs
  Without Sensor Mode: FortiAP only monitors for specific attack signatures
  With Sensor Mode: FortiAP actively tracks client associations with all APs
  → Builds picture of which clients are connected to which APs (authorized or not)
```

**Step 3 — Enable Rogue AP Detection**

```
WIDS Profile → Enable: Rogue AP Detection

What this does:
  Enables the comparison of detected APs against your authorized AP list
  Unknown APs flagged as "rogue" candidates
  Their on-wire status determined by checking client traffic on wired network
  Without this: WIDS never classifies APs as rogue → no suppression possible
```

**Step 4 — Auto-Suppress Rogue APs in Foreground Scan**

```
WIDS Profile → Enable: Auto-suppress Rogue APs in Foreground Scan

What this does:
  Enables AUTOMATIC suppression (without admin intervention)
  When on-wire rogue AP detected → FortiGate automatically sends suppression frames
  Suppression: Deauthentication frames sent to rogue AP's clients
  → Clients disconnected from rogue AP
  → They re-associate with legitimate APs
```

---

### How Rogue AP Suppression Works — Technical Mechanics

```
1. Rogue AP plugged into network switch (on-wire)
2. Rogue AP broadcasts SSID "Free-WiFi"
3. Employee's phone associates with "Free-WiFi" (rogue)

4. FortiAP in Dedicated Monitor mode detects:
   "Unknown BSSID aa:bb:cc:dd:ee:ff broadcasting 'Free-WiFi'"
   "Client phone MAC ff:ee:dd:cc:bb:aa associated with rogue AP"

5. FortiGate checks: Is client ff:ee:dd:cc:bb:aa on our wired network?
   → Checks ARP table, DHCP leases → YES, client IP seen on wired network
   → CONFIRMED: Rogue AP is on-wire (connected to our switch)

6. Auto-suppress enabled → FortiGate instructs monitoring AP:
   "Send spoofed deauthentication frames to client ff:ee:dd:cc:bb:aa
    from rogue BSSID aa:bb:cc:dd:ee:ff"

7. Client phone receives deauthentication → disconnects from rogue AP
8. Client phone scans → finds legitimate FortiAP → associates correctly
9. Process repeats continuously → rogue AP's clients always kicked off
```

> ⚠️ **Legal Note:** Wireless suppression (sending spoofed deauthentication frames) is a legally sensitive area in some jurisdictions. In most enterprise environments, suppressing rogue APs on your own network is permissible. However, suppressing APs in neighboring networks (even accidentally) may violate telecommunications law. **Always verify legal compliance before enabling auto-suppression** in your deployment region.

---

### Manual vs. Automatic Suppression — Trade-offs

| Feature                    | Manual Suppression                | Auto Suppression                                 |
|----------------------------|-----------------------------------|--------------------------------------------------|
| **Speed**                  | Minutes to hours (requires admin) | **Seconds** (immediate)                          |
| **Admin burden**           | High — must log in, identify, act | None — automated                                 |
| **Control**                | ✅ Admin reviews before acting     | ❌ No per-AP review                               |
| **False positive risk**    | Low — admin verifies              | Higher — system may auto-suppress legitimate APs |
| **After-hours protection** | ❌ No protection if no admin       | ✅ 24/7 continuous                                |

> ⚠️ **Trainer Warning:** Auto-suppression can cause problems if a neighbor's legitimate AP is mistakenly classified as a rogue on your network. Always maintain an accurate **authorized AP whitelist** to prevent false positives. Review the rogue AP list regularly and mark neighbor APs as "acknowledged/external" rather than suppressing them.

---

---

# 📄 PAGE 229 — Provisioning the WIDS Profile (Attack Types)

---

## 🖥️ SLIDE SUMMARY

The slide lists **all detectable intrusion attack types** in the WIDS profile:

**Full list of WIDS-detectable attacks:**

| Attack                            | Default Threshold                        |
|-----------------------------------|------------------------------------------|
| **ASLEAP attack**                 | SSID "ASLEAP" in beacon/probe frames     |
| **Association frame flooding**    | 30 requests in 10 seconds                |
| **Authentication frame flooding** | 30 requests in 10 seconds                |
| **Broadcasting deauthentication** | Any spoofed deauth broadcast             |
| **EAPOL packet flooding**         | EAPOL-FAIL, LOGOFF, START, SUCC floods   |
| **Invalid MAC OUI**               | First 3 bytes not in IEEE OUI database   |
| **Long duration attack**          | \> 8,200 microseconds (default)          |
| **Null SSID probe response**      | SSID field empty in probe response       |
| **Spoofed deauthentication**      | Deauth not from legitimate AP            |
| **Weak WEP IV detection**         | Known weak WEP initialization vectors    |
| **Wireless Bridge**               | Both FromDS and ToDS fields set in frame |

**GUI Path:** `WiFi & Switch Controller > WIDS Profiles`

> 📌 **Note:** Additional configurable options available in CLI: `config wireless-controller wids-profile`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Deep Dive — Every Attack Type Explained

---

#### 🔓 ASLEAP Attack

**What ASLEAP is:**

- A tool used to perform **dictionary attacks against LEAP (Cisco Lightweight EAP)** authentication
- LEAP is a deprecated Cisco-proprietary EAP method with known cryptographic weaknesses
- ASLEAP captures the LEAP exchange and cracks the password offline

**How WIDS detects it:**

- The ASLEAP tool **inserts its own SSID identifier** ("ASLEAP") into beacon frames and probe request frames during the attack process
- WIDS looks for the string "ASLEAP" appearing in SSID information elements
- Detection is based on the tool's own signature in the frames

> 💡 **Trainer Note:** This detection is specific to the ASLEAP tool's behavior — not all LEAP attacks. If LEAP is still in use in your environment, migrate to PEAP or EAP-TLS immediately — LEAP is fundamentally broken regardless of ASLEAP detection.

---

#### 🌊 Association Frame Flooding

**What it is:**

- A **DoS (Denial of Service) attack** using massive volumes of 802.11 Association Request frames
- Attacker sends thousands of association requests per second from fake MAC addresses
- AP's association table fills up → legitimate clients cannot associate → wireless network goes down

**Detection threshold:**

- Default: **30 association requests in 10 seconds** from a single source
- Configurable based on your environment's legitimate peak traffic

**Why it's effective as an attack:**

```
Normal client:
  Sends 1-2 association requests → associates → done

Attacker:
  Sends 1000 association requests/second from spoofed MACs
  → AP tracks each "client" → fills association state table
  → Table exhausted → new legitimate clients rejected
  → Wireless DoS achieved
```

---

#### 🌊 Authentication Frame Flooding

**What it is:**

- Similar to association flooding but targets the **802.11 Authentication phase** (which comes before association)
- Floods the AP with authentication request frames from fake MAC addresses
- Exhausts the AP's authentication state table

**Detection threshold:** Same as association flooding — **30 requests in 10 seconds** by default

---

#### 🚫 Broadcasting Deauthentication (Spoofed Deauthentication)

**What it is:**

- One of the most commonly used wireless attacks
- Attacker sends **spoofed deauthentication frames** with the source MAC address of the legitimate AP
- Frames sent to the **broadcast address (FF:FF:FF:FF:FF:FF)** — affects ALL clients simultaneously
- ALL clients receive "you are deauthenticated" and disconnect immediately
- Results in complete wireless outage for all clients

**Why this attack works (WPA2 vulnerability):**

- In WPA2 without PMF, **management frames are NOT authenticated**
- Any device can forge a deauthentication frame claiming to be any AP
- Clients have no way to verify the frame is legitimate
- This is one reason WPA3 **mandates PMF** — protected management frames are cryptographically signed and cannot be spoofed

**WIDS detection:**

- Monitors for deauthentication frames sent to broadcast address
- Checks source MAC — if MAC is not from a known authorized AP → flagged as spoofed

> ⚠️ **Trainer Note:** This attack is trivially easy to launch using tools like `aireplay-ng`. A script kiddie with a $20 USB Wi-Fi adapter can take down an entire enterprise wireless network. Enabling WPA3 with PMF (Protected Management Frames) is the **technical countermeasure** — WIDS provides **detection and logging**.

---

#### 🔑 EAPOL Packet Flooding

**What it is:**

- **EAPOL (EAP over LAN)** packets are used in WPA/WPA2/WPA3 authentication
- An attacker floods the AP with large numbers of EAPOL packets
- Types of EAPOL packets targeted:
  - **EAPOL-START** — triggers authentication restart
  - **EAPOL-LOGOFF** — forces authentication session termination
  - **EAPOL-FAIL** — indicates authentication failure
  - **EAPOL-SUCC** — indicates authentication success

**Attack goal:**

- Exhaust AP authentication resources → prevent legitimate 802.1X authentication
- Force repeated re-authentication cycles → DoS for legitimate clients

---

#### 🔢 Invalid MAC OUI

**What it is:**

- The first **3 bytes (24 bits)** of a MAC address are the **OUI (Organizationally Unique Identifier)**
- Assigned by IEEE to manufacturers: `00:1A:2B` = specific manufacturer
- Some attackers use **randomly generated MAC addresses** to avoid tracking
- Random MAC addresses often have OUI values that **don't correspond to any registered manufacturer**

**WIDS detection:**

- Checks the OUI of client MAC addresses against the **IEEE OUI database**
- OUIs not found in the database → flagged as invalid
- Suggests possible MAC spoofing or automated attack tools using random MACs

**Important caveat:** Modern devices use **MAC address randomization** (iOS, Android, Windows) for privacy. These appear as invalid OUIs. Configure thresholds carefully to avoid false positives from legitimate privacy-protecting devices.

---

#### ⏱️ Long Duration Attack

**What it is:**

- Wi-Fi uses **NAV (Network Allocation Vector)** — a timer that tells other devices how long the channel is reserved
- Normally: NAV values are brief (milliseconds)
- Attack: Set NAV to **extremely large values** (thousands of microseconds)
- Effect: All neighboring devices believe the channel is busy → refuse to transmit → DoS

**Detection threshold:**

- Default: **8,200 microseconds** — any NAV value above this triggers detection
- Range: 1,000 – 32,767 microseconds configurable

---

#### 📡 Null SSID Probe Response

**What it is:**

- When a client sends a **probe request**, it's looking for specific or all SSIDs
- An attacker responds with a **probe response containing a null (empty) SSID**
- Effect: Many wireless drivers and network adapters **crash or stop responding** when they receive a null SSID response
- Results in DoS against wireless clients — their Wi-Fi adapters need a driver restart or system reboot

---

#### 🔓 Weak WEP IV Detection

**What it is:**

- **WEP (Wired Equivalent Privacy)** is a completely broken legacy encryption protocol (deprecated in 2004)
- WEP's weakness: It reuses **Initialization Vectors (IVs)** in a predictable pattern
- By capturing enough WEP frames with **weak IVs**, attackers can statistically recover the WEP key
- Tools like **aircrack-ng** can crack WEP keys in minutes with enough weak IV captures

**WIDS detection:**

- Monitors on-air wireless traffic for frames using known **weak WEP IV patterns**
- Detects when someone is attempting to crack WEP by collecting weak IVs
- Also serves as a reminder that any WEP network in your environment needs immediate upgrade

> ⚠️ **Trainer Warning:** If you see Weak WEP IV alerts in production, it means either (a) someone is trying to crack a WEP network nearby, or (b) you still have WEP networks operating. Either situation requires immediate investigation. **No enterprise network should use WEP in 2024.**

---

#### 🌉 Wireless Bridge Detection

**What it is:**

- In 802.11 frame headers, two bits indicate traffic direction:
  - **ToDS** = Traffic going TO the Distribution System (wired network)
  - **FromDS** = Traffic coming FROM the Distribution System
- Normal wireless client frames: One bit set (either ToDS or FromDS — not both)
- Wireless bridge frames: **BOTH ToDS AND FromDS set simultaneously**

**Why WIDS detects this:**

- Both bits set = wireless frame is being bridged between two wireless networks
- Could indicate: an unauthorized wireless bridge extending your network somewhere unwanted
- Also detects: legitimate mesh/bridge configurations (may generate false positives on your own mesh APs)

> ⚠️ **Important:** If you have intentionally configured wireless mesh or bridge mode in your network — this detection WILL trigger alerts for your own APs. Add your authorized mesh/bridge APs to the authorized list to prevent false positive alerts.

---

---

# 📄 PAGE 230 — Applying WIDS

---

## 🖥️ SLIDE SUMMARY

The slide explains how to **apply WIDS profiles to AP profiles**:

**Key rules:**

| Rule                                | Detail                                                                                   |
|-------------------------------------|------------------------------------------------------------------------------------------|
| **One profile per radio**           | Can only apply ONE WIDS profile to each radio                                            |
| **Profile reuse**                   | Same WIDS profile can be applied to multiple radios and AP profiles                      |
| **Radio mode affects capabilities** | AP in Access Point mode = background scan only; Dedicated Monitor mode = foreground scan |

**GUI Path:** `WiFi & Switch Controller > AP Profiles`

**Key note:**

- **FortiAP not in dedicated monitor mode** → cannot perform foreground scanning
- Level of WIDS participation depends on the **AP's operating mode**

---

## �� DETAILED INSTRUCTOR NOTES

### The WIDS Profile Application Hierarchy

```
WIDS Profile (detection settings + thresholds)
         ↓ applied to
AP Profile (radio configuration)
         ↓ applied to
FortiAP devices (physical hardware)
         ↓
Actual wireless monitoring on radio medium
```

If ANY step is missing → WIDS does not function:

```
WIDS Profile created ✅
AP Profile created ✅
WIDS Profile applied to AP Profile ❌ (FORGOTTEN)
→ FortiAP has NO WIDS capabilities → threats undetected
```

---

### One WIDS Profile Per Radio — Implication

```
FortiAP-231F (2 radios):
  Radio 1 (2.4 GHz): WIDS Profile = "Standard-WIDS"     ← one profile
  Radio 2 (5 GHz):   WIDS Profile = "High-Security-WIDS" ← different profile

FortiAP-443K (3 radios):
  Radio 1 (2.4 GHz): WIDS Profile = "Standard-WIDS"
  Radio 2 (5 GHz):   WIDS Profile = "Standard-WIDS"      ← same profile on both
  Radio 3 (Dedicated Monitor): WIDS Profile = "Full-Scan-WIDS" ← dedicated profile
```

You can customize WIDS behavior **per frequency band** by assigning different profiles to different radios.

---

### Radio Operating Mode and WIDS Capability Matrix

| Radio Mode            | Background Scan         | Foreground Scan            | On-Wire Suppression | Client Service |
|-----------------------|-------------------------|----------------------------|---------------------|----------------|
| **Access Point**      | ✅ Periodic (every 600s) | ❌ Not possible             | ❌ Not possible      | ✅ Full         |
| **Dedicated Monitor** | ✅ Continuous            | ✅ Continuous, all channels | ✅ Possible          | ❌ None         |
| **Disabled**          | ❌ No                    | ❌ No                       | ❌ No                | ❌ None         |

> 💡 **Trainer Tip:** This table reveals the fundamental design trade-off in wireless security:
>
> - **Client service radio** = good coverage + limited security monitoring
> - **Dedicated monitor radio** = no client service + maximum security monitoring
> - Solution: **3-radio APs** — two radios serve clients, one radio dedicated to monitoring → no compromise on either service OR security

---

### Best Practice — WIDS Deployment Architecture

**Small deployment (2-radio FortiAPs only):**

```
All radios in Access Point mode
Apply WIDS profile to both radios
→ Background scanning on all radios
→ Basic threat detection and rogue AP discovery
→ No on-wire suppression
→ Acceptable for low-security environments
```

**Medium deployment (mix of 2-radio and 3-radio FortiAPs):**

```
2-radio APs: Both radios = Access Point + WIDS (background)
3-radio APs: Radio 1+2 = Access Point + WIDS, Radio 3 = Dedicated Monitor + Full WIDS
→ Continuous monitoring coverage from 3-radio APs
→ Background coverage from 2-radio APs
→ On-wire detection from 3-radio monitoring radios
```

**High-security deployment (compliance-driven — PCI-DSS, HIPAA, government):**

```
3-radio APs on all floors with Dedicated Monitor mode on Radio 3
Full WIDS profile applied to all monitoring radios
Auto-suppress enabled for on-wire rogue APs
All alerts forwarded to FortiAnalyzer for correlation
SIEM integration for compliance reporting
→ Continuous, comprehensive wireless threat monitoring
→ Automated response to rogue APs
→ Full audit trail for compliance
```

---

### Default Detection Settings — All Enabled on New Profile

The instructor notes state: *"By default, all of these options are enabled when you create a new WIDS profile."*

This means:

- Creating a new WIDS profile → all attack detections are **ON** by default
- Only need to **adjust thresholds** to tune sensitivity
- Lower thresholds = more sensitive (more alerts, possible false positives)
- Higher thresholds = less sensitive (fewer alerts, may miss slow attacks)

**Starting point for threshold tuning:**

```
1. Deploy with default thresholds
2. Monitor for 2 weeks
3. Identify any false positives (legitimate traffic being flagged)
4. Increase thresholds for false-positive attack types
5. Reduce thresholds for attack types you're most concerned about
6. Review quarterly and adjust based on threat landscape changes
```

---

---

# 📊 Master Summary — Pages 226–230

```
┌──────────────────────────────────────────────────────────────────────────┐
│              WIDS — WIRELESS INTRUSION DETECTION SYSTEM                  │
├─────────────────────────┬────────────────────────────────────────────────┤
│ TOPIC                   │ KEY FACTS                                      │
├─────────────────────────┼────────────────────────────────────────────────┤
│ WIDS Overview           │ Detects wireless-specific Layer 2 attacks      │
│                         │ Works on raw 802.11 frames — not IP traffic     │
│                         │ Logs attacks → FortiAnalyzer can alert/respond │
│                         │ WIDS profile MUST be applied to AP profile     │
│                         │ Complementary to FortiGate UTM (not a replace) │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Background Scanning     │ AP switches to monitor mode every 600 seconds  │
│ (Access Point Mode)     │ Periodic — not continuous                      │
│                         │ Passive scan only — no probe requests sent      │
│                         │ Detects nearby rogue APs                       │
│                         │ Clients briefly interrupted during each scan   │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Foreground Scanning     │ Requires DEDICATED MONITOR radio mode          │
│ (On-Wire Detection)     │ Continuous — scans ALL channels                │
│                         │ Four required settings to enable:              │
│                         │  1. Radio in Dedicated Monitor mode            │
│                         │  2. Sensor Mode in WIDS profile                │
│                         │  3. Rogue AP Detection in WIDS profile         │
│                         │  4. Auto-suppress in WIDS profile              │
│                         │ ⚠️ Steps 1-3 needed even for MANUAL suppression│
├─────────────────────────┼────────────────────────────────────────────────┤
│ Suppression Methods     │ Manual: Admin marks rogue → enables suppress   │
│                         │ Auto: Immediate — no admin needed (24/7)       │
│                         │ Mechanism: Spoofed deauth frames to clients    │
│                         │ ⚠️ Verify legal compliance in your region      │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Attack Types (11 total) │ ASLEAP → LEAP attack tool signature           │
│                         │ Assoc/Auth flooding → DoS (30 reqs/10 secs)  │
│                         │ Broadcasting deauth → All clients drop (PMF!) │
│                         │ EAPOL flooding → DoS on auth (4 packet types) │
│                         │ Invalid MAC OUI → MAC spoofing/random MACs    │
│                         │ Long duration → NAV abuse >8200 microseconds  │
│                         │ Null SSID → Crashes wireless drivers          │
│                         │ Spoofed deauth → Clients disconnected         │
│                         │ Weak WEP IV → WEP cracking attempt            │
│                         │ Wireless Bridge → Both ToDS+FromDS set        │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Applying WIDS           │ One WIDS profile per radio maximum             │
│                         │ Same profile can be shared across radios       │
│                         │ Access Point mode → background scan only       │
│                         │ Dedicated Monitor mode → full foreground scan  │
└─────────────────────────┴────────────────────────────────────────────────┘

⚠️  CRITICAL RULES TO REMEMBER:
  1. WIDS profile does NOTHING until applied to AP profile
  2. Background scan = periodic (600s); Foreground = continuous
  3. On-wire auto-suppression needs ALL 4 settings enabled
  4. Dedicated Monitor mode = no client service (plan hardware)
  5. Deauth broadcast = use WPA3+PMF as countermeasure
  6. Wireless Bridge detection triggers on your OWN mesh APs too
  7. All attacks enabled by default in new WIDS profile — tune thresholds
  8. Legal review required before enabling auto-suppression
```

---

### Topic: Rogue APs and FortiPresence — Rogue AP Detection & Suppression

---

---

# 📄 PAGE 236 — Section Objectives

---

## 🖥️ SLIDE SUMMARY

This is the **section intro page** for Lesson 05: Rogue APs and FortiPresence.

**After completing this section, you will be able to:**

- Learn how to **monitor rogue access points (APs)**
- Understand how to use **scanning methods** available on FortiOS

---

## 📋 DETAILED INSTRUCTOR NOTES

### What This Section Covers — Big Picture

Lesson 05 focuses on a critical real-world wireless security challenge: **what happens when unauthorized APs appear in or around your network?**

The lesson answers three fundamental questions:

1. **What** is a rogue AP — and what isn't?
2. **How** does FortiOS detect rogue APs (two scanning methods)?
3. **What** can FortiOS do about rogue APs (suppression)?

This knowledge is essential for anyone responsible for enterprise wireless security — rogue APs are one of the most common and dangerous wireless threats in practice, and they require both technical and procedural responses.

---

---

# 📄 PAGE 237 — Rogue APs — Definition and Context

---

## 🖥️ SLIDE SUMMARY

The slide provides the **precise definition of a rogue AP** and important context:

**Definition:**

> A rogue AP is an **unauthorized AP connected to YOUR wired network**

**Critical clarifications:**

| Scenario                                                 | Is It a Rogue AP?           | Security Threat?                             |
|----------------------------------------------------------|-----------------------------|----------------------------------------------|
| AP connected to your wired network without authorization | ✅ YES — Rogue               | 🔴 **Yes — unauthorized network access**     |
| Neighbor's home/business AP in same area                 | ❌ NO — External             | ⚠️ Interference only — not a security threat |
| AP in a large warehouse far from perimeter               | ⚠️ Suspicious — investigate | 🔴 Likely a security threat                  |

**Two problems a rogue AP can cause:**

1. **Signal interference** — prevents clients from using your legitimate wireless network
2. **Unauthorized access** — if connected to your wired network, provides a backdoor into your infrastructure

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Precise Definition — Why It Matters

The slide's definition is very specific: *"A rogue AP is an unauthorized AP **connected to your wired network**."*

This single phrase — **connected to your wired network** — is what makes an AP a genuine security threat, not just an inconvenience.

**Understanding the three categories:**

#### Category 1 — Neighbor/External AP (NOT a rogue)

```
Scenario: Office building, floor 5
  Neighbor company on floor 4 has their own wireless network
  Their APs appear in your FortiAP scan results
  They're broadcasting their SSID "Neighbor-Corp-WiFi"
  
Threat assessment:
  ❌ NOT connected to your network
  ❌ NOT a security threat
  ⚠️ MAY cause RF interference (co-channel or adjacent channel)
  
Action: Mark as "Accepted" in FortiGate → stops appearing as unknown
```

#### Category 2 — Rogue AP (TRUE security threat)

```
Scenario: Office building
  Disgruntled employee buys a cheap wireless router
  Plugs it into an Ethernet port in a server room or under a desk
  Sets up an open SSID: "FreeWiFi"
  
Threat assessment:
  ✅ CONNECTED to your wired network
  ✅ Provides unauthorized network access to anyone in range
  ✅ Bypasses all your perimeter security controls
  ✅ May be used to steal data, install malware, or pivot deeper
  
Action: Detect → Mark as Rogue → Suppress → Physically remove
```

#### Category 3 — Evil Twin / Attacker-Placed AP

```
Scenario: Large enterprise campus
  Attacker gains brief physical access → plants a small AP in a ceiling tile
  AP clones your legitimate SSID "Corp-WiFi" and similar BSSID
  Broadcasts at high power to attract clients
  
Threat assessment:
  ✅ May or may not be on-wire (depends on attacker's access)
  ✅ Intercepts wireless client credentials (evil twin attack)
  ✅ Can perform man-in-the-middle attacks
  🔴 Critical threat — immediate suppression required
  
Action: Dedicated monitor detection → Auto-suppress → Physical hunt
```

> 💡 **Real-world analogy:** External neighbor APs are like someone parking their car near your building — they don't have access to your premises. A rogue AP is like someone secretly installing a copy of your front door key in an unmarked door — they've created a hidden entrance into your building that bypasses all your security.

---

### The Warehouse Example — Why Location Context Matters

The slide uses the **warehouse example** deliberately:

```
Scenario: Company has a large warehouse — 200 meters from any neighboring building
  Unknown AP detected in the middle of the warehouse

Analysis:
  Could this belong to a neighbor? 
  → No — neighbors are 200+ meters away, out of normal Wi-Fi range
  
  Could this be an accidental deployment?
  → Unlikely in a warehouse with no IT staff permanently on-site
  
  Verdict: HIGHLY suspicious — likely deliberately placed rogue AP
  → Immediate investigation required
  → Potential physical security breach
```

**Context changes the analysis completely:**

- Same AP in downtown office = probably neighbor → low concern
- Same AP in isolated warehouse = almost certainly rogue → high concern

> ⚠️ **Trainer Note:** This is why **on-site knowledge** matters in wireless security. An automated system can detect unknown APs — a human with knowledge of the physical environment makes the judgment call about whether it's truly a rogue or an external AP.

---

---

# 📄 PAGE 238 — Rogue AP Detection Overview

---

## 🖥️ SLIDE SUMMARY

The slide explains the **two detection methods** available on FortiAP:

| Method                     | How It Works                                                                        |
|----------------------------|-------------------------------------------------------------------------------------|
| **During idle periods**    | Background scan — radio briefly switches to monitor during client service idle gaps |
| **As a dedicated monitor** | Foreground scan — full radio(s) dedicated to continuous monitoring                  |

**FortiOS automatic response:**

- Detects rogue AP → **automatically adds to Rogue APs list**
- Using WIDS profile with dedicated monitor mode → can **suppress the rogue AP**

**Flexibility note:**

- Can use **one radio for scanning**, other radio for normal AP service
- Trade-off: limits you to **one frequency band** on the scanning radio (2.4 GHz OR 5 GHz — not both)

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Two-Radio Strategy — Splitting Functions

On a **2-radio FortiAP**, you can split radio duties:

```
Option A — Both radios serve clients (no dedicated monitoring):
  Radio 1: 2.4 GHz → serves clients + background scan (periodic)
  Radio 2: 5 GHz → serves clients + background scan (periodic)
  → Maximum client capacity
  → Minimum monitoring effectiveness

Option B — Split function:
  Radio 1: 2.4 GHz → serves clients
  Radio 2: DEDICATED MONITOR → continuous scanning
  → One band serves clients (reduced capacity)
  → One radio does full-time security monitoring
  → Best for security-focused deployments with enough APs for coverage

Option C — Both radios in dedicated monitor (monitoring-only AP):
  Radio 1: DEDICATED MONITOR (2.4 GHz scanning)
  Radio 2: DEDICATED MONITOR (5 GHz scanning)
  → No client service on this AP
  → Maximum monitoring coverage
  → Requires other APs to cover clients
  → Used in high-security environments (PCI-DSS compliance)
```

---

### The Frequency Limitation — Why It Matters

The slide notes: *"You can use only one band per radio."*

This means if Radio 2 is set to dedicated monitor mode for **2.4 GHz scanning**:

- It can only scan **2.4 GHz channels**
- 5 GHz rogue APs in the area will **not be detected** by this radio
- To detect both bands → need both a 2.4 GHz and a 5 GHz scanning radio

**For 3-radio FortiAPs (Wi-Fi 6/6E):**

```
Radio 1: 2.4 GHz → serves clients
Radio 2: 5 GHz → serves clients
Radio 3: Dedicated Monitor → scans BOTH 2.4 GHz and 5 GHz (rotates through channels)
→ Full client service + full monitoring
→ The ideal configuration
```

---

---

# 📄 PAGE 239 — Background Scanning

---

## 🖥️ SLIDE SUMMARY

The slide details **background scanning** behavior:

**Characteristics:**

| Property                  | Value/Behavior                                            |
|---------------------------|-----------------------------------------------------------|
| **Scan timing**           | During idle intervals between AP client service           |
| **Packet loss risk**      | ✅ Can cause packet loss when radio switches to monitoring |
| **Band coverage**         | Only the frequency band of that radio                     |
| **Discovery speed**       | **Slower** rogue discovery                                |
| **Automatic activation**  | Yes — enabled automatically when **DARRP is enabled**     |
| **Scan period**           | Every **600 seconds** by default                          |
| **Per-channel scan time** | **20 ms** per channel                                     |

---

## 📋 DETAILED INSTRUCTOR NOTES

### Background Scanning — Complete Technical Mechanics

**The detailed 600-second cycle:**

```
t = 0:
  Radio operating normally → serving clients on Channel 6 (2.4 GHz)
  Clients transmitting/receiving data

t = 600 seconds (scan period begins):
  Radio switches from ACCESS POINT mode → MONITOR mode
  
  Channel 1 scan: Listen for 20ms → record all BSSIDs heard → RSSI values noted
  Channel 2 scan: Listen for 20ms → record all BSSIDs heard
  Channel 3 scan: Listen for 20ms
  ...continue through all channels for that band...
  Channel 11 scan: Listen for 20ms (final channel for 2.4 GHz in USA)
  
  Total scan time: 11 channels × 20ms = ~220ms of monitoring
  
  Radio switches back → ACCESS POINT mode
  Resumes serving clients

t = 600 + (scan duration):
  Back to normal client service
  Scan results reported to FortiGate via CAPWAP control channel
  FortiGate compares detected BSSIDs against authorized AP list

t = 1200 seconds:
  Next scan cycle begins → repeats
```

---

### The Packet Loss Problem — Why It Matters

When the radio switches to monitor mode, even briefly:

- Clients trying to transmit data during those **\~220ms** get **no response from the AP**
- TCP sessions experience the gap as **packet loss** or **retransmission needed**
- VoIP/video calls may notice **brief audio/video glitches**
- For **heavily loaded APs**, this interruption is more noticeable

**Conditions that worsen packet loss during background scan:**

```
Heavy traffic AP: Many clients transmitting simultaneously
  → More packets in flight when scan begins
  → More packets dropped during the ~220ms switch
  → More TCP retransmissions needed
  → Noticeable degradation

Lightly loaded AP: Few clients, low traffic
  → Few packets in flight during scan
  → Minimal or no noticeable impact
```

> 💡 **Real-world analogy:** Background scanning is like a toll booth attendant who periodically steps away from their booth for 30 seconds to check the surrounding area for suspicious vehicles. Cars that arrive during those 30 seconds have to wait. In low-traffic periods, no one notices. During rush hour, a queue builds quickly.

---

### DARRP and Background Scanning — The Link

The slide states: *"Automatic if DARRP is enabled."*

**Why DARRP enables background scanning:**

- DARRP needs to **scan neighboring channels** to evaluate which channel has the least interference
- This scanning process is the **same physical operation** as background scanning for rogue APs
- When DARRP is enabled → the radio already performs background scans for channel selection
- FortiOS **piggybacks** rogue AP detection onto these existing DARRP scans → no additional overhead
- Result: Enabling DARRP provides "free" background scanning for rogue AP detection

> ⚠️ **Trainer Note:** Even though DARRP enables background scanning, the slide describes it as "offers poor rogue AP detection." This is because the scan is **periodic and brief** — a rogue AP that is only intermittently active might not be broadcasting during the 20ms scan window and would be missed.

---

---

# 📄 PAGE 240 — Dedicated Monitor Mode

---

## 🖥️ SLIDE SUMMARY

The slide explains **dedicated monitor mode** — full-time scanning:

**Characteristics:**

| Property                 | Behavior                                                         |
|--------------------------|------------------------------------------------------------------|
| **Scanning type**        | Continuous **foreground** scans                                  |
| **Rogue AP suppression** | ✅ Can send deauthentication frames                               |
| **Client service**       | ❌ **Cannot serve any wireless clients**                          |
| **Discovery speed**      | **Faster** rogue discovery                                       |
| **Best practice**        | Have **at least one AP dedicated to monitoring** in your network |

---

## 📋 DETAILED INSTRUCTOR NOTES

### Dedicated Monitor Mode — Technical Operation

In dedicated monitor mode, the radio:

```
Continuous Channel Rotation:
  Channel 1 (2.4 GHz): Scan → record BSSIDs, RSSI, client associations
  Channel 2 (2.4 GHz): Scan → record
  ...
  Channel 11 (2.4 GHz): Scan → record
  Channel 36 (5 GHz): Scan → record (if dual-band monitor)
  Channel 40 (5 GHz): Scan → record
  ...and so on continuously...
  → Returns to Channel 1 → repeats
  
No pauses for client service
No SSID broadcasting
No association acceptance
100% of radio time dedicated to monitoring
```

**What gets collected:**

- All BSSID (AP MAC addresses) detected and their SSIDs
- All client MAC addresses seen associating with each BSSID
- RSSI values for each detected device
- Channel and band information
- Frame types observed (management, data, control)

All data sent to FortiGate via CAPWAP → stored in rogue AP database → available in GUI

---

### Why Dedicated Monitor Enables Suppression

The slide states: *"Allows you to suppress rogue APs by sending deauthentication frames."*

**Why background scan CANNOT suppress:**

```
Background scan AP:
  Primary role: Serving clients → CANNOT stop broadcasting own SSID
  When monitoring: Brief 20ms windows → not continuously on rogue AP's channel
  To suppress: Must stay on rogue AP's channel and continuously send deauth frames
  → Impossible while still serving clients
  → Cannot suppress
```

**Why dedicated monitor CAN suppress:**

```
Dedicated monitor AP:
  Primary role: Monitoring → Already on rogue AP's channel continuously
  No clients to serve → full radio time available for suppression
  Can switch to rogue AP's exact channel → stay there → continuously transmit deauth frames
  → Full suppression capability
```

---

### The "One Dedicated Monitor AP" Best Practice

The slide states: *"It is a good idea to have one AP dedicated to monitoring in your network."*

**Why one dedicated monitor AP benefits ALL other APs:**

```
Without dedicated monitor AP:
  Every serving AP does background scanning
  Every AP experiences the brief scan-related packet loss
  Every AP has reduced monitoring effectiveness

With one dedicated monitor AP (e.g., placed in center of floor):
  All other APs: 100% client service, no interruption
  Monitor AP: 100% scanning, no client service
  → ALL client-serving APs perform better (no scan interruption)
  → Monitoring is continuous and comprehensive (no coverage gaps)
  → Best of both worlds
```

> 💡 **Real-world analogy:** Having one dedicated monitoring AP is like a security team with one member dedicated to watching all the security cameras while others patrol. The patrollers can focus entirely on their patrol route without stopping to check cameras — the camera watcher has full-time visibility of everything.

---

---

# 📄 PAGE 241 — On-Wire Rogue AP Detection — Foreground Scanning

---

## 🖥️ SLIDE SUMMARY

The slide explains the **on-wire detection mechanism** and its two methods:

**The core concept:**

- Compares **wireless and wired client traffic** to identify rogue APs connected to your network
- Requirement: At least **one Wi-Fi client** connected to the suspect AP AND **continuously sending traffic**

**Two on-wire detection methods:**

| Method              | How It Works                                             | Limitation                  |
|---------------------|----------------------------------------------------------|-----------------------------|
| **Exact MAC match** | Same MAC seen on both wired AND wireless → AP is on-wire | Defeated by NAT on rogue AP |
| **MAC adjacency**   | Similar (adjacent) MAC addresses matched                 | False positives possible    |

**Complications:**

- **MAC address spoofing** makes detection harder
- **NAT on the rogue AP** hides client MACs on wired side
- **MAC adjacency** is configurable: `set rogue-scan-mac-adjacency {0-31}` (default: **7**)

**GUI Path:** `Dashboard > WiFi > Rogue APs`

---

## 📋 DETAILED INSTRUCTOR NOTES

### On-Wire Detection — The 4-Step Process

```
Step 1: Dedicated monitor radio listens wirelessly
  → Hears rogue AP BSSID: "aa:bb:cc:dd:ee:ff" broadcasting "FreeWiFi"
  → Sees client MAC: "11:22:33:44:55:66" associated with rogue AP
  → Records all associations with unknown BSSIDs

Step 2: FortiAP collects wired MAC addresses
  → Monitors ARP requests and replies on wired network segment
  → Builds a table of all MAC addresses seen on the wired LAN
  → Reports this table to FortiGate controller via CAPWAP

Step 3: FortiGate compares wireless and wired tables
  → Wireless side: Client "11:22:33:44:55:66" is associated with unknown BSSID
  → Wired side: Client "11:22:33:44:55:66" also appears in ARP table
  → Match found → client is simultaneously on wireless (via rogue AP) AND wired network

Step 4: Conclusion and action
  → If client is on wired network → it got there through an AP
  → If that AP is not authorized → rogue AP is on-wire
  → FortiGate logs alert: "On-wire rogue AP detected: aa:bb:cc:dd:ee:ff"
  → If auto-suppress enabled → suppression begins
```

---

### Exact MAC Address Match — The Clean Case

**Normal operation (Layer 2 bridged rogue AP):**

```
Rogue AP connected to switch (Layer 2 bridge mode):
  Rogue AP's WLAN: Client phone MAC = "11:22:33:44:55:66"
  Switch sees: Frame from "11:22:33:44:55:66" → Client phone's MAC passed through
  
FortiGate:
  Wireless: Client "11:22:33:44:55:66" → associated with rogue BSSID "aa:bb:cc:dd:ee:ff"
  Wired: MAC "11:22:33:44:55:66" → seen in ARP table on wired network
  → EXACT MATCH → On-wire confirmed
```

**Why this is the clearest detection method:**

- No ambiguity — same MAC address appearing on both wireless and wired
- No false positives from MAC adjacency
- Definitive proof the rogue AP is bridging traffic to your wired network

---

### MAC Adjacency — Handling the NAT Case

**The NAT problem:**

```
Sophisticated rogue AP (NAT/router mode):
  Client phone MAC "11:22:33:44:55:66" connects wirelessly to rogue AP
  Rogue AP applies NAT → on wired network:
    Source MAC = rogue AP's WAN MAC "aa:bb:cc:dd:ee:01"
    NOT the client's actual MAC
  
FortiGate:
  Wireless: Client "11:22:33:44:55:66" → rogue BSSID "aa:bb:cc:dd:ee:ff"
  Wired: Only sees "aa:bb:cc:dd:ee:01" (rogue AP's wired MAC)
  → NO exact match → detection fails with exact MAC method alone
```

**MAC adjacency solves this:**

```
Observation: Most devices use consecutive (adjacent) MAC addresses 
  for their different interfaces

Example rogue AP:
  Wireless BSSID (beacon): "aa:bb:cc:dd:ee:ff"
  Wired Ethernet MAC:      "aa:bb:cc:dd:ee:01"
  Difference: ff vs 01 → hexadecimal difference = 254

MAC adjacency setting (default = 7):
  Maximum hex difference allowed for a match = 7
  ff - f8 = 7 (would match)
  ff - 01 = 254 (would NOT match with default of 7)
  
  Increase adjacency setting to catch wider MAC differences:
  set rogue-scan-mac-adjacency 15 → catches differences up to 15
  set rogue-scan-mac-adjacency 31 → maximum → catches differences up to 31
```

---

### MAC Adjacency — False Positive Risk

The slide warns: *"False positives are a possibility in MAC adjacency."*

**How false positives occur:**

```
Scenario: Two completely different manufacturers happen to have
  adjacent MAC addresses in their OUI ranges
  
Device A (legitimate client):
  Wireless MAC: "00:1A:2B:CC:DD:EE" (connected to your AP)
  Wired MAC:    "00:1A:2B:CC:DD:EF" (connected to switch — this is a DIFFERENT physical device)
  
Hex difference: EE vs EF = 1 → within adjacency threshold of 7

False positive:
  FortiGate sees "00:1A:2B:CC:DD:EE" wirelessly → associated with your legitimate AP
  FortiGate sees "00:1A:2B:CC:DD:EF" on wired network → legitimate device
  MAC adjacency match → FortiGate incorrectly concludes "00:1A:2B:CC:DD:EE" is on-wire via a rogue AP
```

**Mitigation:**

- Keep the adjacency value LOW (default 7 is conservative)
- Review on-wire alerts manually before taking action
- Investigate physically before suppressing based on MAC adjacency match

---

### `rogue-scan-mac-adjacency` — CLI Configuration

```bash
config wireless-controller setting
    set rogue-scan-mac-adjacency 7    # Default — conservative
end

# Range: 0 to 31
# 0 = exact match only (most accurate, least false positives)
# 31 = most aggressive (catches NAT rogue APs better, more false positives)
```

**Recommended approach:**

- Start with default (7) and monitor for false positives
- Increase gradually only if you know you have NAT-mode rogue APs that aren't being detected
- Investigate every MAC adjacency alert manually before suppressing

---

---

# 📄 PAGE 242 — Suppressing Rogue APs

---

## 🖥️ SLIDE SUMMARY

The slide explains the **rogue AP suppression mechanism**:

**Requirements:**

- ✅ **Dedicated monitor mode** required — background scan cannot suppress
- ✅ Uses **on-wire detection** to confirm target
- ⚠️ **Check laws and regulations** of your region before enabling

**How suppression works (dual deauthentication attack):**

```
FortiGate/FortiAP pretends to be:       Sends deauth TO:
  The ROGUE AP (spoofed source MAC)  →  Rogue AP's clients
  The rogue AP's CLIENTS (spoofed)   →  The Rogue AP itself
```

**Result:** Rogue AP's clients constantly disconnected → impossible to maintain connection

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Suppression Mechanism — Technical Deep Dive

Suppression works by exploiting the **same WPA2 management frame weakness** that attackers use in deauthentication DoS attacks — but in reverse, against the rogue AP.

**Direction 1 — FortiAP mimics the rogue AP:**

```
FortiAP (Dedicated Monitor) transmits:
  802.11 Deauthentication Frame:
    Source MAC: [rogue AP's BSSID] ← spoofed
    Destination MAC: [rogue AP's client] ← broadcast or specific client
    Reason Code: 1 (Unspecified)
    
Effect on client:
  Client receives deauth from "its AP" (the rogue)
  → Disconnects from rogue AP
  → Attempts to reconnect
  → Immediately receives another deauth
  → Cannot maintain connection to rogue AP
```

**Direction 2 — FortiAP mimics the rogue's clients:**

```
FortiAP (Dedicated Monitor) transmits:
  802.11 Deauthentication Frame:
    Source MAC: [rogue AP's client MAC] ← spoofed
    Destination MAC: [rogue AP's BSSID]
    Reason Code: 1 (Unspecified)
    
Effect on rogue AP:
  Rogue AP thinks its client is disconnecting
  → Clears client association entry
  → When client reconnects → rogue AP must re-process → immediate deauth again
  → Association table constantly cleared → rogue AP cannot maintain any clients
```

**Combined effect:**

```
Rogue AP → receives deauth from "clients" → clears associations
Rogue AP's clients → receive deauth from "rogue AP" → disconnect and retry
Constant loop → rogue AP effectively neutralized
→ Users cannot connect to rogue AP AND stay connected
→ They eventually give up or associate with legitimate FortiAPs
```

> 💡 **Real-world analogy:** Suppression is like a building security team that intercepts guests heading to an unauthorized room. From the guest's side, they receive a message saying "the room is closed." From the unauthorized room's side, they receive messages saying "all guests have checked out." Both parties are confused and the unauthorized gathering never takes place.

---

### Legal Considerations — Critical Warning

The slide prominently states: *"Check laws and regulations of your region before enabling suppression."*

**Why this is a serious legal concern:**

**In your own network:** Generally permissible in most jurisdictions — you have authority to prevent unauthorized access to your own infrastructure.

**Near neighbor networks:** If your suppression accidentally targets APs belonging to neighboring businesses or homes (false positive detection), you may be:

- **Disrupting communications** — potentially illegal under telecommunications law
- **Causing business harm** — potential civil liability
- **Violating FCC regulations** (USA) / **Ofcom regulations** (UK) / **ARCEP** (France) etc.

**Key laws to be aware of:**

```
USA: Communications Act of 1934, Section 333
  → Prohibits willful or malicious interference with radio communications
  
EU: Directive 2014/53/EU (Radio Equipment Directive)
  → Prohibits interference with other radio equipment

Many countries have similar provisions in their telecommunications laws
```

**Best practice before enabling suppression:**

1. Consult your legal team
2. Obtain written authorization from network owner
3. Configure accurate authorized AP whitelist to prevent false positives
4. Document the business justification for suppression
5. Maintain logs of all suppression events

> ⚠️ **Trainer Warning:** This is not theoretical — companies have faced legal action for deploying "rogue AP suppression" that accidentally affected neighboring Wi-Fi networks. Never enable auto-suppression without legal review and a properly maintained authorized AP list.

---

---

# 📄 PAGE 243 — Rogue AP Monitor

---

## 🖥️ SLIDE SUMMARY

The slide shows the **Rogue AP Monitor** — the central management interface for discovered APs:

**Location:** `Dashboard > WiFi > Rogue APs`

**AP states available:**

| State                   | Meaning                                       | Security Impact                              |
|-------------------------|-----------------------------------------------|----------------------------------------------|
| **Accepted AP**         | Acknowledged — trusted external or authorized | No action taken                              |
| **Rogue AP**            | Marked as unauthorized/suspicious             | Designation only — does NOT block by default |
| **Suppressed Rogue AP** | Active suppression enabled                    | FortiAP actively sends deauth frames         |

**Key warning from slide:**

- Marking as "Accepted" or "Rogue" is **only a tracking designation**
- It does **NOT affect the ability** to use these APs
- To actively block → must select **"Suppressed Rogue AP"**

**On-wire indicator:**

- "On-wire" column shows detection by MAC address match

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Rogue AP Monitor — Functional Overview

```
Dashboard > WiFi > Rogue APs shows:

 ┌─────────────────────────────────────────────────────────────────────┐
 │ BSSID         │ SSID          │ Band │ Channel │ RSSI │ On-Wire │ State    │
 ├───────────────┼───────────────┼──────┼─────────┼──────┼─────────┼──────────┤
 │ aa:bb:cc:dd.. │ FreeWiFi      │ 2.4G │ 6       │ -65  │ YES     │ Rogue    │
 │ cc:dd:ee:ff.. │ NeighborCorp  │ 5G   │ 36      │ -78  │ NO      │ Accepted │
 │ ff:ee:dd:cc.. │ ???           │ 2.4G │ 11      │ -55  │ YES     │ Rogue    │
 └─────────────────────────────────────────────────────────────────────┘
```

**Decision workflow for each discovered AP:**

```
Unknown AP appears in list
         ↓
Is this AP on-wire? (check On-wire column)
         ↓
NO → Is it a neighbor's AP?     YES → IMMEDIATE security concern
         ↓                                ↓
      YES → Mark "Accepted"       Mark "Rogue"
      NO  → Investigate more      Enable Suppression
                                  Physically locate and remove
```

---

### The Critical Distinction — Marking vs. Suppressing

This is the most commonly misunderstood aspect of the Rogue AP Monitor:

**Marking as "Rogue" in the GUI:**

```
Action: Click AP → Mark as Rogue
Effect:
  → AP appears in "Rogue" category in the GUI
  → FortiGate logs the designation change
  → The rogue AP continues broadcasting normally
  → Clients can STILL connect to it
  → NO network access restriction applied
  → This is PURELY an administrative label for tracking
```

**Enabling Suppression:**

```
Action: Click AP → Suppress Rogue AP
Effect:
  → FortiGate instructs monitoring radio to begin suppression
  → Deauthentication frames sent to rogue AP and its clients
  → Clients cannot maintain connection to rogue AP
  → Actual network access disruption occurs
  → This is the ACTIVE security response
```

> 💡 **Real-world analogy:** Marking an AP as "Rogue" is like writing "UNAUTHORIZED VEHICLE" on a car window in your parking lot. The car is still there and still running — you've just labeled it. Enabling suppression is like calling a tow truck — the car is now actually being removed.

---

### The "Accepted" State — Why It's Important

Marking external APs as "Accepted" serves a critical operational purpose:

```
Without marking as Accepted:
  Neighbor's AP "NeighborCorp-WiFi" appears EVERY SCAN as unknown
  Security team must investigate it EVERY TIME
  Alert fatigue → real rogue APs get lost in noise

With marking as Accepted:
  "NeighborCorp-WiFi" still detected every scan
  Shown in "Accepted" category → not flagged as suspicious
  Only NEW unknown APs trigger alerts
  → Security team focuses on genuinely unknown/suspicious APs
```

**The Accepted state is an acknowledgment — not a security control:**

- You're saying "I know about this AP — it's not mine but it's not a threat"
- It still appears in the list (you haven't hidden it)
- You can change it back to unknown or rogue at any time
- No traffic inspection or blocking of that AP's clients

---

### Additional Rogue AP Detection Features

The slide notes that the Rogue AP Monitor is not the only detection mechanism in FortiOS:

```
FortiOS rogue AP detection ecosystem:
  1. Rogue AP Monitor (Dashboard > WiFi > Rogue APs)
     → The primary GUI-based detection and classification tool
     
  2. Audit Grade Reports
     → Scheduled reports showing all detected APs
     → Historical data for compliance auditing
     → Tracks new APs appearing over time
     
  3. On-Wire Correlation
     → MAC address comparison (exact + adjacency)
     → Identifies rogue APs connected to your wired network
     
  4. Automatic Suppression
     → WIDS profile-based auto-suppression
     → No admin intervention needed for on-wire rogues
     
  5. Wireless IDS (WIDS)
     → Broader wireless threat detection
     → Detects attack patterns, not just unauthorized APs
     → Logs attack signatures for forensic analysis
```

> 💡 **Trainer Tip:** For comprehensive rogue AP defense, use **all five mechanisms together**:
>
> - WIDS for attack detection
> - On-wire correlation for definitive rogue confirmation
> - Auto-suppression for immediate response
> - Rogue AP Monitor for operational management and classification
> - Audit reports for compliance documentation

---

---

# 📊 Master Summary — Pages 236–243

```
┌──────────────────────────────────────────────────────────────────────────┐
│          ROGUE AP DETECTION & SUPPRESSION — COMPLETE GUIDE               │
├──────────────────────────┬───────────────────────────────────────────────┤
│ TOPIC                    │ KEY FACTS                                     │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Rogue AP Definition      │ Unauthorized AP CONNECTED TO YOUR WIRED NET  │
│                          │ Neighbor AP = NOT a rogue (external/interference)│
│                          │ Two threats: interference + unauthorized access│
│                          │ Location context determines threat level       │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Background Scanning      │ Periodic — every 600 seconds                  │
│                          │ Each channel: 20ms listen time                │
│                          │ Can cause packet loss during scan             │
│                          │ Auto-enabled when DARRP is enabled            │
│                          │ Scans only radio's own frequency band         │
│                          │ CANNOT suppress rogue APs                     │
│                          │ "Poor" rogue detection per Fortinet           │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Dedicated Monitor        │ Continuous foreground scanning                │
│                          │ Rotates through ALL channels continuously     │
│                          │ NO client service                             │
│                          │ REQUIRED for rogue AP suppression             │
│                          │ Best practice: 1 AP dedicated per deployment  │
├──────────────────────────┼───────────────────────────────────────────────┤
│ On-Wire Detection        │ Compares wireless client MACs vs wired MACs  │
│                          │ Exact match: Same MAC on wire and wireless    │
│                          │ MAC adjacency: Similar hex MACs (default: 7) │
│                          │ CLI: set rogue-scan-mac-adjacency {0-31}     │
│                          │ NAT on rogue AP defeats exact match           │
│                          │ MAC adjacency can have false positives        │
│                          │ Requires dedicated monitor mode               │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Suppression              │ REQUIRES dedicated monitor mode               │
│                          │ Dual deauth: mimics rogue AP to its clients  │
│                          │                mimics clients to rogue AP    │
│                          │ ⚠️ CHECK LOCAL LAWS BEFORE ENABLING          │
│                          │ Cannot suppress with background scan          │
│                          │ False positives may affect neighbor networks  │
├──────────────────────────┼───────────────────────────────────────────────┤
│ Rogue AP Monitor         │ Dashboard > WiFi > Rogue APs                 │
│                          │ States: Unknown/Accepted/Rogue/Suppressed     │
│                          │ ⚠️ Marking as Rogue = LABEL ONLY             │
│                          │ ⚠️ Actual blocking = Suppressed Rogue AP      │
│                          │ On-wire column = confirmed on your network    │
│                          │ Mark neighbors as Accepted → reduce noise     │
└──────────────────────────┴───────────────────────────────────────────────┘

⚠️  CRITICAL RULES TO REMEMBER:
  1. Rogue AP = on YOUR wired network — neighbor AP is NOT a rogue
  2. Background scan CANNOT suppress — Dedicated Monitor required
  3. Marking AP as "Rogue" in GUI does NOT block it — Suppression does
  4. ALWAYS verify legal compliance before enabling auto-suppression
  5. MAC adjacency (default=7) can produce false positives — monitor carefully
  6. NAT on rogue AP defeats exact MAC match → use adjacency + manual investigation
  7. One dedicated monitor AP benefits ALL client-serving APs on the floor
  8. Mark known neighbor APs as "Accepted" to reduce alert fatigue
```

---

### Topic: Rogue APs and FortiPresence — FortiPresence Analytics Platform

---

---

# 📄 PAGE 247 — FortiPresence Overview

---

## 🖥️ SLIDE SUMMARY

The slide introduces **FortiPresence** — Fortinet's presence analytics platform:

**Two deployment platforms:**

| Platform                | Type             | Description                                         |
|-------------------------|------------------|-----------------------------------------------------|
| **FortiPresence Cloud** | SaaS cloud-based | Part of FortiLAN Cloud — no on-site hardware needed |
| **FortiPresence VM**    | On-premises      | Runs as virtual machines locally on your site       |

**Key characteristics:**

- Supports the **majority of Fortinet wireless devices**
- Delivers **real-life business intelligence**
- Particularly applicable to **SD-branch scenarios**
- FortiPresence Cloud is part of **FortiLAN Cloud** ecosystem

---

## 📋 DETAILED INSTRUCTOR NOTES

### What Is FortiPresence? — The Business Problem It Solves

FortiPresence answers a question that businesses with physical locations have always wanted to answer:

> *"What are visitors doing in my physical space — and how can I use that data to improve my business?"*

**Traditional business analytics:**

- Website analytics: Google Analytics, Adobe Analytics — tells you what visitors do ONLINE
- Physical space analytics: Manual counters, security cameras — tells you basic footfall

**FortiPresence analytics (using existing Wi-Fi infrastructure):**

- How long do visitors stay in each area? (dwell time)
- How often do they return? (visit frequency)
- Which areas are most crowded? (heat maps)
- What devices do they use? (device type breakdown)
- What are their demographics? (via social login)
- How does traffic compare between locations? (multi-site comparison)

> 💡 **Real-world analogy:** Your FortiAP deployment is like having a network of invisible observers throughout your physical space — they were already there to provide Wi-Fi, but FortiPresence gives those observers the ability to report meaningful business intelligence about every visitor who walks in.

---

### SaaS Cloud vs. On-Premises VM — When to Choose Each

**FortiPresence Cloud (SaaS):**

```
Best for:
  → Small-to-medium businesses
  → Multi-site deployments managed centrally
  → Organizations with FortiLAN Cloud already deployed
  → SD-branch scenarios (distributed sites, no local servers)
  → Organizations wanting zero infrastructure to manage

Trade-offs:
  → Data goes to Fortinet's cloud → data privacy considerations
  → Requires internet connectivity for AP data upload
  → Cloud infrastructure availability dependent
```

**FortiPresence VM (On-Premises):**

```
Best for:
  → Large enterprises with data sovereignty requirements
  → Healthcare, finance, government — strict data residency laws
  → Organizations with existing on-premises infrastructure
  → Deployments requiring full data control
  → High-security environments where cloud data upload is prohibited

Trade-offs:
  → Requires server infrastructure (2 VMs)
  → Internal IT team must manage the platform
  → Updates must be managed manually
```

---

### SD-Branch — Why FortiPresence Is Applicable

**SD-Branch (Software-Defined Branch)** is Fortinet's architecture for distributed branch offices:

- Each branch runs FortiGate, FortiSwitch, FortiAP — all managed centrally
- FortiPresence integrates naturally — APs at each branch already deployed
- One FortiPresence instance provides analytics across ALL branches simultaneously
- Central visibility into visitor behavior across hundreds of locations

**Example:** A retail chain with 200 stores — each store has FortiAPs. FortiPresence aggregates visitor analytics from all 200 stores into one dashboard — showing which stores have the most foot traffic, longest dwell times, most return visitors, etc.

---

---

# 📄 PAGE 248 — FortiPresence — How It Works

---

## 🖥️ SLIDE SUMMARY

The slide shows the **complete data flow** of FortiPresence in 5 steps:

```
Step 1: Smartphone emits Wi-Fi probe signal
        (even if NOT connected — just in visitor's pocket)
         ↓
Step 2: FortiAP/FortiWiFi captures MAC address + signal strength
         ↓
Step 3: Onsite APs or FortiLAN Cloud summarizes + forwards data records
         ↓
Step 4: FortiPresence service receives data
         (pushed via SSL connection every 10 seconds — configurable)
         ↓
Step 5: Analytics engine processes data → Dashboard displays actionable format
```

**Data collected includes:**

- Dwell time
- Visit frequency
- Visit duration
- Foot traffic patterns
- Visitor density
- Location comparisons

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Wi-Fi Probe Signal — The Technical Foundation

**This is the core technical insight that makes FortiPresence possible:**

When a smartphone (or any Wi-Fi-enabled device) is powered on — even if it's in a pocket and the owner has no intention of connecting to Wi-Fi — the device's Wi-Fi radio continuously **broadcasts probe request frames**:

```
Probe Request Frame:
  Source MAC: [Device's MAC address] ← unique identifier per device
  SSID: "" (null) or [specific SSIDs the device has connected to before]
  Supported rates: [what Wi-Fi standards the device supports]
  
This frame is broadcast:
  Every few seconds (varies by OS and settings)
  On multiple channels in rotation
  Can be heard by ANY FortiAP within radio range
```

**Why probe requests enable presence analytics:**

- The MAC address is a **unique device identifier**
- FortiAP can detect the device's **signal strength (RSSI)** → approximates distance
- Multiple APs detecting the same MAC → **triangulation** → approximate location
- Repeated detection → **visit duration and frequency** tracking

> 💡 **Real-world analogy:** Every smartphone constantly broadcasts a unique "I'm here" radio beacon. FortiAPs are like invisible listeners that hear these beacons and report to FortiPresence. The visitor never needs to connect to Wi-Fi — their device's automatic probing provides the data.

---

### MAC Address Randomization — The Modern Challenge

> ⚠️ **Critical trainer note:** Modern operating systems (iOS 14+, Android 10+, Windows 10+) now use **MAC address randomization** for privacy protection. Instead of using the device's permanent hardware MAC, they use a **randomly generated MAC** for each Wi-Fi network/probe session.

**Impact on FortiPresence:**

```
Old behavior (pre-2020):
  Device always uses same MAC → reliable unique identifier → accurate analytics

New behavior (2020+):
  Device generates new random MAC every 24 hours or per SSID/scan
  → Same physical device appears as different visitor each day
  → Visit frequency analytics inaccurate
  → Return visitor cannot be recognized
```

**FortiPresence's mitigation approaches:**

- Correlating with **social login** data (email/phone → links random MACs over time)
- **Analytics algorithms** that handle randomized MACs
- Using **CONNECTED client data** (when client is associated, FortiGate sees the actual MAC)
- This is an evolving area — Fortinet continues to improve MAC randomization handling

---

### The 10-Second Data Push — Why It Matters

The slide states data is pushed every **10 seconds (configurable)**.

**Why 10 seconds?**

- Near-real-time analytics — dashboards reflect current conditions with minimal delay
- Not instantaneous (avoids overloading FortiPresence with constant tiny updates)
- Not too slow (60+ seconds would make heat maps and real-time views inaccurate)

**What gets pushed every 10 seconds:**

- All new probe request detections
- Updated RSSI readings for detected devices
- Associated client information (if connected to SSID)
- AP location data for triangulation

**Configurable range:** Can be increased (less network overhead, less real-time accuracy) or decreased (more overhead, more real-time) based on deployment needs.

---

### The SSL Connection — Security

All data between FortiAP and FortiPresence travels over an **SSL (TLS)-secured connection**:

- Prevents interception of visitor MAC addresses and location data
- Privacy protection for visitors' device information
- Encrypts analytics data in transit
- Certificate validation ensures data goes only to the legitimate FortiPresence instance

---

---

# 📄 PAGE 249 — FortiPresence VM

---

## 🖥️ SLIDE SUMMARY

The slide details the **FortiPresence VM** — the on-premises deployment option:

**Architecture — Two VMs required:**

| VM                        | Purpose                                               |
|---------------------------|-------------------------------------------------------|
| **Infrastructure Server** | SQL database service — stores all analytics data      |
| **Application Server**    | GUI access — web interface for dashboards and reports |

**Data flow:**

```
FortiGate / FortiLAN Cloud
         ↓ station reports (visitor data)
Infrastructure Server (SQL) ←→ Application Server (GUI)
         ↓
FortiPresence Dashboard (browser-based)
```

**Key features:**

- **End-to-end analytics** — complete control over data
- **All Fortinet wireless APs supported** — FortiGate-managed AND FortiLAN Cloud-managed
- **Customizable dashboards** — real-time + animated maps + video playback
- **Google Maps integration** for site location analysis
- **Social login** support: Facebook, email, SMS-OTP for guest Wi-Fi captive portal

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Two-VM Architecture — Why Two Separate VMs?

**Infrastructure separation is intentional:**

```
VM 1 — Infrastructure Server (SQL):
  Runs: Microsoft SQL Server or compatible database
  Stores: All raw analytics data, visitor records, station reports
  Access: Backend only — not directly accessed by end users
  Sizing: Larger storage requirements — all historical data lives here
  
VM 2 — Application Server:
  Runs: FortiPresence web application
  Provides: Web-based GUI (dashboard, reports, site management)
  Accesses: Queries VM1's SQL database for analytics data
  Access: HTTPS browser access by administrators and analysts
  Sizing: Moderate compute — processes queries and renders dashboards
```

**Benefits of separation:**

- **Performance:** Database operations don't compete with web server processes
- **Security:** Database server not directly internet-accessible
- **Scalability:** Can scale each VM independently (more storage for DB, more CPU for app)
- **Backup:** Database VM backed up independently from application VM

---

### Google Maps Integration — Business Value

The slide highlights Google Maps integration:

- Each **site is pinned on a real map** — search for visitor analytics by geographic location
- **Multi-site operators** can click any location → view that site's analytics
- Excellent for **retailers, restaurant chains, hotel groups** with dozens/hundreds of locations
- Visual correlation: "Our downtown locations have 40% more foot traffic than suburban locations"

---

### Visitor Authentication Options in FortiPresence VM

The slide mentions three social/guest login methods:

**1. Social Media Authentication (Facebook, Google, etc.)**

- Guest clicks "Login with Facebook" on captive portal
- FortiPresence receives: name, email, age range, gender (with user permission)
- Links MAC address to a named person → resolves MAC randomization partially
- Provides **demographic analytics** (age groups, gender distribution)

**2. Email Authentication**

- Guest enters email address → receives verification link/code
- FortiPresence stores email → long-term visitor tracking possible
- Privacy-friendly: only email, no social media permissions needed

**3. SMS-OTP Authentication**

- Guest enters phone number → receives one-time password via SMS
- Phone number verification → demographic data
- High engagement: ensures visitor provides real contact details

---

---

# 📄 PAGE 250 — FortiPresence Dashboard

---

## 🖥️ SLIDE SUMMARY

The slide shows the **FortiPresence analytics dashboard** interface:

**Dashboard characteristics:**

- **Summary view** of presence analytics
- **Customizable graphical** representation of visitor, device, and site analytics
- Configurable for **specific locations and date ranges**
- Shows **aggregate trends** over configured time periods
- **Panels can be rearranged** to suit different roles (store manager vs. marketing analyst)

**Data flow to dashboard:**

```
APs → FortiGate/FortiLAN Cloud → Raw station reports →
FortiPresence analytics engine → Summary charts/statistics →
Dashboard (fetched when accessed)
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### Dashboard Architecture — How Data Gets Displayed

**Important technical point:** The dashboard does NOT process data in real-time on demand:

```
Continuous background process:
  APs send station reports every 10 seconds
  FortiPresence analytics engine processes raw data continuously
  Results compiled into summary charts and statistics
  Statistics stored in database

When admin accesses dashboard:
  Browser requests dashboard data
  Application server fetches pre-computed statistics from database
  Renders charts and graphs using stored summaries
  → Dashboard loads quickly (no heavy computation on access)
  → Data is current as of last processing cycle (near real-time)
```

**Why this architecture matters:**

- Dashboard access is **fast** — no waiting for big data computation
- Multiple users can access simultaneously without performance impact
- Historical data is pre-computed — old date ranges load just as fast as today's data

---

### Customizable Panels — Role-Based Dashboard Views

Different business stakeholders need different views:

```
Store Manager Dashboard:
  → Today's visitor count vs. yesterday
  → Current peak hours (real-time)
  → Busiest areas on floor plan
  
Marketing Team Dashboard:
  → Return visitor rate (loyalty metric)
  → Demographics breakdown (age/gender from social login)
  → Campaign impact: foot traffic before/after promotion
  
Operations Manager Dashboard:
  → Multi-site comparison
  → Longest dwell time areas (queue indicators)
  → Peak traffic hours for staffing decisions
```

FortiPresence allows each user to configure their own panel layout — the same underlying data serves different business functions.

---

---

# 📄 PAGE 251 — FortiPresence Analytics

---

## 🖥️ SLIDE SUMMARY

The slide lists the **specific analytics metrics** available:

**Six core analytics types:**

| Metric                      | Business Question It Answers                               |
|-----------------------------|------------------------------------------------------------|
| **Dwell time**              | How long do visitors stay in each area?                    |
| **Bounce rate**             | How many visitors leave almost immediately?                |
| **Engagement**              | How actively are visitors interacting with the space?      |
| **Floor widget**            | Where are visitors located on the floor plan right now?    |
| **Device type breakdowns**  | What devices (iPhone, Android, laptop) are visitors using? |
| **Demographics breakdowns** | What are the age/gender profiles of visitors?              |

**Analytical capability:**

- Compare **current data with historical records**
- Recognize **trends as they occur**
- Correlate data with **events and business initiatives**
- Dashboard calculates **average statistics** over selected time range

---

## 📋 DETAILED INSTRUCTOR NOTES

### Each Metric — Business Application Deep Dive

---

#### 📊 Dwell Time

**Technical definition:** Duration from first detection of a device's MAC at a location to last detection before it leaves.

**Business applications:**

```
Retail store:
  High dwell time in clothing section → engagement is high
  Low dwell time at checkout → efficient queue management
  Very high dwell time in electronics → staff may need to engage more

Restaurant:
  High dwell time at tables → good (people are eating, satisfied)
  High dwell time at entrance → bad (waiting too long, need more capacity)
  
Museum/attraction:
  Per-exhibit dwell time → which exhibits are most engaging?
  → Data-driven exhibit curation decisions
```

---

#### 📊 Bounce Rate

**Technical definition:** Percentage of visitors who are detected only once or for a very short time (e.g., less than 2 minutes) — they "bounced" without meaningfully engaging with the space.

**Business applications:**

```
Retail:
  High bounce rate at store entrance → window display not compelling
  → Redesign entrance display
  → A/B test different window displays, compare bounce rates

Hotel lobby:
  High bounce rate → check-in process too long?
  → Visitors walking in, seeing queue, leaving
  
Event venue:
  High bounce rate during keynote → speaker not engaging?
  → Visitors checking email, stepping outside
```

---

#### 📊 Engagement

**Technical definition:** Combination metric — considers dwell time, return visits, and area coverage. Higher engagement = visitor spent meaningful time and explored the space.

**Business interpretation:**

- A visitor who spends 90 minutes, visits 5 different areas, and returns 3 times = HIGH engagement
- A visitor who passes through in 5 minutes and never returns = LOW engagement

---

#### 📊 Floor Widget

**Technical definition:** Real-time heat map overlay on the floor plan showing **current visitor density** per area.

**Business applications:**

```
Retail right now (live view):
  Red area (hot): 50 people in electronics section
  Blue area (cold): 0 people in luggage section
  
Operations response:
  → Move staff from luggage to electronics
  → Open additional checkout lanes in response to crowd buildup
  → Real-time staffing decisions
  
Event management:
  → Security can see overcrowded areas in real-time
  → Redirect attendees to less crowded areas
  → Emergency evacuation: see where people currently are
```

---

#### 📊 Device Type Breakdowns

**Technical definition:** Classification of detected devices into categories based on Wi-Fi probe characteristics (manufacturer OUI, supported rates, capabilities).

**Analytics provided:**

```
Example data:
  iPhone: 45%
  Android: 38%
  Windows laptop: 12%
  Other: 5%
```

**Business applications:**

- **Retail:** Majority iPhone users → optimize mobile shopping app for iOS first
- **Event:** High laptop percentage → ensure power outlets and tables for working
- **Airport:** Mix of all devices → ensure Wi-Fi compatibility across all standards

---

#### 📊 Demographics Breakdowns

**Technical definition:** Age and gender distribution of visitors — available ONLY from social login data (Facebook, Google, etc.).

```
Example data (from social login):
  18-24: 23%
  25-34: 41%
  35-44: 22%
  45+: 14%
  
  Male: 52%
  Female: 46%
  Other: 2%
```

**Business applications:**

- **Retail:** 41% in 25-34 → target marketing campaigns at this demographic
- **Restaurant:** Evening crowd younger than lunch crowd → different menu promotions
- **Event:** Mostly 45+ → adjust content, pace, and technology complexity accordingly

> ⚠️ **Privacy trainer note:** Demographics data from social login is **opt-in** — visitors choose what information they share when logging in via social media. Ensure your captive portal clearly explains what data is collected and how it's used. GDPR (Europe), CCPA (California), and other privacy regulations apply to this data.

---

---

# 📄 PAGE 252 — FortiPresence Location Analytics

---

## 🖥️ SLIDE SUMMARY

The slide covers **location analytics** capabilities:

**Key features:**

- Collects location data from **FortiGate** and **FortiLAN Cloud**
- Includes: **analytics, heat maps, and reporting**
- Offers **captive portal-based Wi-Fi access** with multiple login options:
  - Facebook
  - Email
  - Phone number

**Use case environments:**

- Stores
- Shopping malls
- Airports
- Any venue with visitor foot traffic

**Business value:**

- Gain insight into visitor **behaviors within the site** — real-time and historical
- Improve **business efficiencies**
- Improve **visitor experiences**
- Positive **bottom-line impact**

---

## �� DETAILED INSTRUCTOR NOTES

### Heat Maps — The Visual Intelligence Tool

Heat maps are FortiPresence's most visually compelling feature:

```
What a heat map shows:
  Floor plan image (uploaded by admin)
  Color overlay representing visitor density:
    🔴 Red (hot): Very high density → crowded area
    🟡 Yellow: Moderate density → normal traffic
    🟢 Green: Low density → quiet area
    🔵 Blue: Very low/no visitors
    
Animated playback:
  See how the heat map changes over time
  "Video" of a full day's visitor movement patterns
  → Morning rush at entrance
  → Lunchtime cluster in food area
  → Afternoon spread to merchandise areas
  → Evening crowd at checkout
```

**Triangulation for positioning:**

```
Multiple FortiAPs detect same visitor device:
  AP-1 (north): RSSI = -65 dBm → approximately 10m away
  AP-2 (south): RSSI = -75 dBm → approximately 25m away
  AP-3 (east):  RSSI = -70 dBm → approximately 18m away
  
Triangulation calculation:
  Using RSSI from 3+ APs → estimate device position on floor plan
  Accuracy: typically 3-10 meters (depends on AP density and environment)
  
Result: Visitor dot placed on heat map at triangulated position
```

---

### Captive Portal Integration — The Data Enrichment Strategy

The combination of **location analytics + captive portal login** creates powerful data:

```
Without captive portal:
  Know THAT a device visited → WHERE → HOW LONG
  Don't know WHO the device belongs to

With captive portal (Facebook login):
  Know THAT John Smith (age 32, male) visited
  AND WHERE he went in the store
  AND HOW LONG he stayed in each department
  AND how many times he's returned this month
  
Enriched data enables:
  Personalized marketing: Email John about products in areas he visited
  Loyalty programs: Reward frequent visitors automatically
  A/B testing: Compare visitor behavior before/after layout changes
```

---

---

# 📄 PAGE 253 — FortiPresence Reports

---

## 🖥️ SLIDE SUMMARY

The slide explains **FortiPresence reporting capabilities**:

**Report types:**

- Different **site and visitor reports** available
- Can cover different **time periods** and **geographic regions**

**Export formats:**

- 📄 **CSV** — for further analysis in Excel, BI tools
- 📑 **PDF** — for sharing with stakeholders, management presentations

**Scheduling options:**

- Built at a **scheduled time** (daily, weekly, monthly)
- Automatically sent **by email** to designated recipients

**Historical tracking:**

- **Historical list** of all generated reports maintained

---

## 📋 DETAILED INSTRUCTOR NOTES

### Report Types Available

**Visitor Reports:**

```
New vs. returning visitors over time period
Peak hours analysis (hour-by-hour)
Average dwell time per area
Bounce rate trends
Demographics summary (if social login used)
```

**Network Reports:**

```
Total unique devices detected
Connected clients (actually on Wi-Fi)
Device type distribution
Wi-Fi vs. passerby (connected vs. probe-only)
```

**Site Reports:**

```
Per-floor traffic analysis
Area-by-area dwell time comparison
Multi-site comparison (requires paid license)
Peak day/time identification
```

---

### Scheduled Reports — Operational Value

```
Automated weekly store manager report:
  Every Monday 7:00 AM → report generated for previous week
  → Emailed to store manager, district manager, and marketing
  → Contains: visitor count, peak hours, popular areas, bounce rate
  → Compared to same week previous year

  Store manager arrives Monday morning:
  → Already has last week's performance summary in inbox
  → No manual data extraction required
  → Acts on insights before opening time
```

---

### CSV Export — Integration with Business Intelligence

CSV export enables integration with existing business analytics tools:

```
FortiPresence → Export CSV → Import into:
  Microsoft Power BI → Custom visualizations
  Tableau → Advanced analytics
  Google Data Studio → Marketing dashboards
  SAP Analytics → Enterprise BI
  Excel → Ad-hoc analysis by non-technical staff
```

This allows organizations to **combine FortiPresence data** with other business data (sales figures, staffing schedules, promotional calendars) for deeper analysis.

---

---

# 📄 PAGE 254 — FortiPresence Location Services Configuration

---

## 🖥️ SLIDE SUMMARY

The slide explains **how to register APs with FortiPresence**:

**Registration components:**

- **Project name** — auto-generated by FortiPresence
- **Secret key** — auto-generated by FortiPresence
- **FortiPresence IP address and port** — FortiPresence VM details

**Where to find registration info:**

| Platform                | Path                                               |
|-------------------------|----------------------------------------------------|
| **FortiPresence Cloud** | `Administration > AP Management > License Details` |
| **FortiPresence VM**    | `Admin > Settings > Discovered APs`                |

**How AP links to FortiPresence:**

```
Managed by FortiGate → Configure in FortiAP profile
Managed by FortiLAN Cloud → Configure in Location Service settings
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### Registration Architecture — The Link Between AP and FortiPresence

```
FortiPresence creates a PROJECT:
  Project name: "RetailStore-Downtown" (auto-generated)
  Secret key: "xK9mP2rT4vN8..." (auto-generated, cryptographically random)
  
This project info is entered in:
  FortiAP profile (if FortiGate-managed):
    config wireless-controller vap
      or
    config wireless-controller wtp-profile
      set fortipresence [cloud/custom]
      set fortipresence-server [FortiPresence IP]
      set fortipresence-port [port]
      set fortipresence-project [project name]
      set fortipresence-secret [secret key]
      
Result:
  FortiAP sends station reports to FortiPresence using:
  → Project name to identify which analytics project data belongs to
  → Secret key for authentication (proves AP is authorized)
  → HTTPS/SSL for data security
```

---

### Why Secret Keys Are Auto-Generated

FortiPresence auto-generates project names and secret keys (rather than letting admins choose):

- **Security:** Random keys are cryptographically strong — not guessable
- **Uniqueness:** No risk of two organizations accidentally using the same key
- **Simplicity:** Admin copies the key from FortiPresence → pastes into FortiGate profile
- **Rotation:** Keys can be regenerated if compromised — FortiPresence generates new ones

> ⚠️ **Trainer Warning:** The secret key is the authentication credential between your APs and FortiPresence. Treat it like a password — store securely, don't share unnecessarily, and regenerate if you suspect compromise.

---

---

# 📄 PAGE 255 — FortiPresence Site Management

---

## 🖥️ SLIDE SUMMARY

The slide shows **site management** in FortiPresence — defining the physical geography:

**Hierarchy of location definition:**

```
Sites (top level — e.g., "Downtown Store")
  └── Buildings (e.g., "Main Building")
        └── Floors (e.g., "Ground Floor")
              └── Areas (e.g., "Electronics Section")
```

**Key features:**

- **Google Maps integrated UI** — locate sites on real-world maps
- Create sites, buildings, floors, and areas
- **Areas require floor definition first** (cannot create area without a floor)

**GUI path:**

- Site Management section of FortiPresence interface

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Site Hierarchy Matters — Analytics Precision

The more precisely you define your physical space, the more precise your analytics:

```
Basic setup (site only):
  "Visitors spent average 22 minutes at Downtown Store"
  → Tells you total dwell time at the site
  → No location detail within the site

Complete setup (sites + buildings + floors + areas):
  "Visitors spent average:
    2 min at entrance
    8 min in electronics section
    4 min in checkout queue
    5 min in clothing section
    3 min in food court"
  → Actionable insights per area
  → Know exactly where attention is needed
```

---

### Google Maps Integration — Multi-Site Management

```
FortiPresence dashboard with Google Maps:

  [Interactive Map of city]
  
  📍 Downtown Store — 1,247 visitors today
  📍 Uptown Mall — 892 visitors today
  📍 Airport Location — 3,421 visitors today
  📍 Suburban Outlet — 567 visitors today
  
  Click any pin → drill down to that site's analytics
  
Benefits:
  → Instantly see which locations are performing
  → Geographic patterns: downtown busier weekdays, suburbs busier weekends
  → Multi-site operations management from one screen
```

---

---

# 📄 PAGE 256 — FortiPresence Site Management (Continued)

---

## 🖥️ SLIDE SUMMARY

The slide shows **floor plan setup details**:

**Floor creation requires:**

- Upload a **floor plan image** (architectural drawing, image file)
- Define area based on **known distances** using red pointer markers on the floor map

**What can be placed on the floor plan:**

- 🖨️ **Fixed assets** (printers, cameras, kiosks) — drag and drop
- 📡 **Discovered APs** — import and position on floor plan

**AP positioning parameters required:**

- **RSSI value** — minimum signal strength cutoff for detection
- **EIRP** — Effective Isotropic Radiated Power — the AP's transmit power

---

## 📋 DETAILED INSTRUCTOR NOTES

### Floor Plan Setup — Step-by-Step Process

```
Step 1: Create Floor
  Name: "Ground Floor"
  Upload image: ground_floor_plan.jpg (architectural drawing)
  Set scale: define real-world distance using two reference points
    "These two points are 20 meters apart"
    → FortiPresence calibrates all distance calculations

Step 2: Define Areas
  Draw area boundaries on the floor plan image:
    Area 1: "Electronics" (draw rectangle over that section)
    Area 2: "Clothing" (draw rectangle)
    Area 3: "Checkout" (draw rectangle near registers)
  
Step 3: Add Fixed Assets
  Drag printer icon → drop on actual printer location
  Drag camera icon → drop on camera location
  → These assets are reference points for visitor tracking accuracy

Step 4: Import APs
  Select FortiAP from discovered AP list
  Drag → drop on actual physical AP location on floor plan
  Enter: Minimum RSSI cutoff value (-70 dBm example)
  Enter: EIRP value (AP's transmit power in dBm)
```

---

### RSSI Cutoff and EIRP — Why These Values Matter

**RSSI Cutoff:**

```
Purpose: Define the maximum detection range for this AP
  Set to: -75 dBm (example)
  Meaning: Only count a visitor detection if the AP sees them at -75 dBm or better
  Effect: Ignores very weak signals from people far from the AP
          → Reduces false detections from adjacent areas
          → Improves location accuracy by limiting each AP's "claim" to nearby visitors
```

**EIRP (Effective Isotropic Radiated Power):**

```
Purpose: Tell FortiPresence the actual RF power output of this AP
  Used for: Calibrating distance calculations based on RSSI
  
Physics: Signal strength follows inverse square law
  RSSI = EIRP - (path loss at distance d)
  
  If FortiPresence knows EIRP → can calculate distance from RSSI
  More accurate distance → more accurate visitor positioning on floor map
```

> 💡 **Real-world analogy:** EIRP and RSSI cutoff are like calibrating a measuring tape. Without knowing how bright the light source is (EIRP), you can't calculate how far away something is based on how bright it appears (RSSI). Setting these correctly turns your Wi-Fi APs into accurate indoor positioning beacons.

---

---

# 📄 PAGE 257 — FortiPresence Social Wi-Fi

---

## 🖥️ SLIDE SUMMARY

The slide covers **Social Wi-Fi** — FortiPresence's captive portal for guest access:

**Social Wi-Fi components:**

- **Captive portal** — intercepts guest Wi-Fi connection
- **Guest Wi-Fi access** — provides internet connectivity after login
- **Social networking credentials** — login with Facebook, Google, etc.
- **Demographic information** — collects visitor profile data with consent

---

## 📋 DETAILED INSTRUCTOR NOTES

### Social Wi-Fi — The Complete Guest Experience

**Step-by-step visitor experience:**

```
Step 1: Visitor enters premises
  Smartphone automatically detects FortiAP broadcasting guest SSID
  Device connects (no password required — open SSID)
  
Step 2: Captive portal redirect
  Visitor opens browser → any website
  FortiGate redirects to FortiPresence captive portal
  Portal displays: Company logo, welcome message, login options
  
Step 3: Visitor chooses authentication method
  Option A: "Login with Facebook" → redirects to Facebook OAuth
  Option B: "Continue with Email" → enter email → verify
  Option C: "Use Phone Number" → enter number → receive SMS code → enter code
  
Step 4: Data collection (with consent)
  Option A (Facebook): FortiPresence requests permissions
    → Basic profile: name, age range, gender (if user permits)
    → Visitor sees exactly what data is shared → can accept or decline
  Option B (Email): Only email address collected
  Option C (Phone): Only phone number collected
  
Step 5: Network access granted
  FortiGate opens captive portal
  Visitor now has internet access
  FortiPresence links their session to their identity (MAC + email/social profile)
  
Step 6: Analytics enrichment
  Visitor's movement through store → linked to named person
  Demographics analysis enabled
  Return visit recognition → "Welcome back, John!"
```

---

### Privacy Compliance — Essential Considerations

Collecting demographic data from social logins triggers privacy regulations:

```
GDPR (EU/EEA):
  → Must have lawful basis for processing (explicit consent)
  → Must clearly explain what data is collected and why
  → Visitor must actively opt-in (pre-ticked boxes NOT allowed)
  → Must allow data deletion on request
  → Data protection officer may be required

CCPA (California):
  → Must disclose data collection at point of collection
  → Must provide opt-out option
  → Cannot sell personal data without explicit consent

General best practices:
  → Plain-language privacy notice on captive portal
  → Granular consent options (basic access vs. marketing)
  → Easy unsubscribe/data deletion process
  → Minimize data collection to what's genuinely needed
```

> ⚠️ **Trainer Warning:** Deploying Social Wi-Fi without proper privacy compliance is a significant legal and reputational risk. Always involve your legal team before enabling social login data collection. GDPR fines can reach €20 million or 4% of global annual turnover.

---

---

# 📄 PAGE 258 — FortiPresence Licensing

---

## 🖥️ SLIDE SUMMARY

The slide compares the **Free vs. Paid tiers** of FortiPresence:

| Feature                                 | Unlicensed (Free) | Licensed (Paid) |
|-----------------------------------------|-------------------|-----------------|
| **Number of sites**                     | **1**             | **Unlimited**   |
| **Number of APs**                       | **15**            | **Unlimited**   |
| **Data retention**                      | **7 days**        | **1 year**      |
| **Concurrent captive portal sessions**  | **200**           | **Unlimited**   |
| **Site reports and user management**    | N/A               | ✅ Yes           |
| **Social media authentication**         | Paid per site     | Paid per site   |
| **Captive portal customization**        | Paid per site     | Paid per site   |
| **Theme and images for captive portal** | ✅ Yes             | ✅ Yes           |

**Notes:**

- Both tiers hosted on **cloud OR on-premises**
- Cloud version: **no additional on-site hardware** needed
- Social media authentication and captive portal customization: **paid per site** on BOTH tiers

---

## 📋 DETAILED INSTRUCTOR NOTES

### Licensing Tiers — Decision Framework

**Free tier is appropriate when:**

```
→ Single location business (1 site limit)
→ Fewer than 15 APs at that location
→ Only need recent data (7 days is sufficient)
→ Up to 200 concurrent guest Wi-Fi users
→ Basic analytics dashboard
→ Want to evaluate FortiPresence before committing
```

**Paid tier becomes necessary when:**

```
→ Multiple locations (more than 1 site)
→ More than 15 APs
→ Need historical trending beyond 7 days
  (regulatory compliance often requires 90-365 days)
→ More than 200 concurrent guest Wi-Fi sessions
  (e.g., mall anchor store with 300+ simultaneous Wi-Fi users)
→ Need site reports for management reporting
→ Need multi-user management (different admins per site)
```

---

### Important Per-Site Costs — Not Included in Base License

Note carefully: Even in the **Paid tier**, these features are **additional per-site costs**:

- **Social media authentication** — paid per site
- **Captive portal customization** — paid per site

This means a retailer with 50 locations wanting social login on all sites must budget for:

- 1 FortiPresence Paid license (unlimited sites)
- PLUS 50 × social media authentication per-site fees
- PLUS 50 × captive portal customization per-site fees

> 💡 **Trainer Tip:** When helping customers size FortiPresence licensing, always clarify whether they need social media login and custom captive portals — these significantly affect total cost. Start with the base paid license + per-site add-ons for the sites that need them most.

---

### Data Retention — Compliance Implications

**7-day retention (Free):**

```
Sufficient for: Operational troubleshooting
Not sufficient for: Trend analysis, compliance auditing
Issue: Cannot see last month's visitor counts → no monthly reporting possible
```

**1-year retention (Paid):**

```
Enables:
  → Year-over-year comparisons (this Christmas vs. last Christmas)
  → Seasonal trend analysis
  → Marketing campaign impact measurement (before/after)
  → Compliance reporting for some industries
  
Most retailers need at least 90 days for meaningful trend analysis
Paid tier's 1-year retention satisfies most business intelligence needs
```

---

---

# 📊 Master Summary — Pages 247–258

```
┌──────────────────────────────────────────────────────────────────────────┐
│              FORTIPRESENCE — COMPLETE GUIDE                              │
├─────────────────────────┬────────────────────────────────────────────────┤
│ TOPIC                   │ KEY FACTS                                      │
├─────────────────────────┼────────────────────────────────────────────────┤
│ What FortiPresence Is   │ Presence analytics platform using Wi-Fi APs   │
│                         │ Two platforms: Cloud (SaaS) + VM (on-premises) │
│                         │ Cloud = part of FortiLAN Cloud                 │
│                         │ VM = two VMs: SQL (infra) + Web (application)  │
├─────────────────────────┼────────────────────────────────────────────────┤
│ How It Works            │ Smartphones emit probe requests (even unpaired)│
│                         │ FortiAP captures MAC + RSSI from all probes   │
│                         │ Data pushed to FortiPresence every 10 seconds  │
│                         │ Via SSL-secured connection                     │
│                         │ Analytics engine processes → dashboard shows   │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Analytics Metrics       │ Dwell time → how long visitors stay           │
│                         │ Bounce rate → visitors who leave quickly       │
│                         │ Engagement → combined activity metric          │
│                         │ Floor widget → real-time heat map             │
│                         │ Device types → iPhone/Android/laptop split     │
│                         │ Demographics → age/gender (from social login)  │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Location Services       │ Heat maps with animated playback               │
│                         │ Triangulation using multiple AP RSSI values   │
│                         │ Accuracy: typically 3-10 meters               │
│                         │ Captive portal: Facebook/Email/SMS-OTP login  │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Reports                 │ Formats: CSV + PDF                            │
│                         │ Scheduling: automated + email delivery         │
│                         │ Types: visitor/network/site/device             │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Site Management         │ Hierarchy: Site → Building → Floor → Area     │
│                         │ Google Maps integration for site locations     │
│                         │ Floor plans uploaded → areas drawn             │
│                         │ AP dragged to position on floor plan           │
│                         │ RSSI cutoff + EIRP configured per AP           │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Registration            │ Auto-generated: project name + secret key     │
│                         │ FortiGate-managed: configure in AP profile     │
│                         │ FortiLAN Cloud: configure in Location Service  │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Social Wi-Fi            │ Captive portal with social login               │
│                         │ Enables demographic data collection            │
│                         │ ⚠️ GDPR/CCPA compliance REQUIRED              │
│                         │ Explicit consent + data deletion rights        │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Licensing               │ FREE: 1 site, 15 APs, 7-day retention, 200 CP │
│                         │ PAID: Unlimited sites/APs, 1-year, unlimited CP│
│                         │ Social media auth = paid PER SITE (both tiers) │
│                         │ Portal customization = paid PER SITE           │
└─────────────────────────┴────────────────────────────────────────────────┘

⚠️  CRITICAL RULES TO REMEMBER:
  1. FortiPresence uses PROBE REQUESTS — device doesn't need to be connected
  2. MAC randomization (iOS 14+/Android 10+) affects analytics accuracy
  3. Demographics data ONLY available if visitor uses social login
  4. Social media auth is PAID PER SITE — factor into cost planning
  5. GDPR/CCPA compliance is MANDATORY before collecting social login data
  6. EIRP + RSSI cutoff must be configured correctly for positioning accuracy
  7. Area analytics require floor definition BEFORE area creation
  8. Free tier: 7-day data retention → no trend analysis possible
  9. Project name + secret key are auto-generated — copy from FortiPresence
```

---

### Topic: Advanced Options and Monitoring — Advanced Wireless Features & Guest Access

---

---

# 📄 PAGE 265 — Advanced Wireless Features Overview

---

## 🖥️ SLIDE SUMMARY

The slide explains how to **access advanced wireless features** in FortiGate:

**How to enable advanced features:**

- Go to `System > Feature Visibility` → enable advanced wireless features
- Some core features (like WIDS) **move** to different GUI sections when advanced mode is on
- Additional profiles become visible and configurable

**Three new advanced profiles added to GUI:**

| Profile Type                       | Where Configured                                 | Where Assigned           |
|------------------------------------|--------------------------------------------------|--------------------------|
| **QoS Profiles**                   | `WiFi & Switch Controller > Operation Profiles`  | SSID interface settings  |
| **L3 Firewall Profiles**           | `WiFi & Switch Controller > Protection Profiles` | SSID interface settings  |
| **FortiAP Configuration Profiles** | `WiFi & Switch Controller > Operation Profiles`  | Managed FortiAP settings |

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Advanced Features Are Hidden by Default

FortiGate's GUI is designed with **progressive disclosure** — show only what most deployments need, hide complexity until it's required:

```
Basic deployment needs:
  → SSIDs, Security, DHCP, Firewall Policies
  → GUI is clean and focused

Advanced deployment needs:
  → QoS for VoIP prioritization
  → L3 firewall for bridge mode access control
  → FortiAP config profiles for local AP settings
  → These add complexity — hidden until opted into
```

This prevents accidental misconfiguration of advanced features by administrators who don't need them.

---

### Feature Visibility — What Changes

When you enable advanced wireless features in `System > Feature Visibility`:

```
Before (simple view):
  WiFi & Switch Controller:
    → SSIDs
    → Managed FortiAPs
    → FortiAP Profiles
    → WIDS Profiles

After (advanced view):
  WiFi & Switch Controller:
    → SSIDs (with advanced options in SSID settings)
    → Managed FortiAPs (with FortiAP config profile assignment)
    → FortiAP Profiles
    → WIDS Profiles (may move to Protection Profiles section)
    → Operation Profiles:
         → QoS Profiles (new)
         → FortiAP Configuration Profiles (new)
    → Protection Profiles:
         → L3 Firewall Profiles (new)
```

> ⚠️ **Trainer Warning:** Enabling Feature Visibility changes **where** some settings appear in the GUI. If an administrator enabled Feature Visibility in the past and a new admin can't find a setting, this is often the cause. Always check Feature Visibility settings when troubleshooting "missing" GUI options.

---

---

# 📄 PAGE 266 — L3 Firewall Profile

---

## 🖥️ SLIDE SUMMARY

The slide shows **L3 Firewall Profiles** — wireless bridge access control:

**What it does:**

- Configures a **bridge access control list (ACL)** for wireless traffic
- Part of **wireless protection profiles**
- Supports **IPv4 and IPv6**
- Assigned in **SSID settings** (advanced section)

**Rules define:**

- **IP address** (source/destination)
- **Port numbers**
- **IANA protocol numbers** (e.g., TCP=6, UDP=17, ICMP=1)

**GUI Path:** `WiFi & Switch Controller > Protection Profiles > L3 Firewall Profiles`

---

## 📋 DETAILED INSTRUCTOR NOTES

### L3 Firewall Profile — Why It Exists

**The Bridge Mode problem:** In Bridge Mode, wireless client traffic exits locally at the AP — it **never reaches FortiGate**. This means:

- FortiGate's standard firewall policies do NOT apply to this traffic
- No security inspection at the FortiGate level for local Bridge Mode traffic
- Without L3 firewall profile → any client can reach any local resource

**L3 Firewall Profile as the solution:**

- Applies access control **at the AP level** — before traffic enters the local network
- Acts as a **lightweight ACL** (Access Control List) enforced by FortiAP itself
- Provides basic allow/deny rules based on IP/port/protocol without needing FortiGate UTM

---

### L3 Firewall Profile vs. Standard FortiGate Firewall Policy

| Feature              | Standard Firewall Policy  | L3 Firewall Profile              |
|----------------------|---------------------------|----------------------------------|
| **Where enforced**   | FortiGate (centralized)   | FortiAP (at the edge)            |
| **Traffic mode**     | Tunnel Mode primarily     | Bridge Mode primarily            |
| **Inspection depth** | Deep (UTM, IPS, AV, etc.) | Basic (IP/port/protocol ACL)     |
| **UTM features**     | ✅ Full UTM available      | ❌ Not available                  |
| **IPv6 support**     | ✅ Yes                     | ✅ Yes                            |
| **Use case**         | All traffic types         | Bridge Mode local access control |

---

### L3 Firewall Rule Structure

```
L3 Firewall Profile: "Bridge-Guest-Control"

Rule 1:
  Action: DENY
  Protocol: ANY
  Source IP: 10.10.2.0/24 (guest subnet)
  Destination IP: 10.10.1.0/24 (corporate subnet)
  Purpose: Block guests from accessing corporate network

Rule 2:
  Action: DENY
  Protocol: TCP
  Source IP: ANY
  Destination IP: ANY
  Port: 22 (SSH)
  Purpose: Block SSH access from wireless clients

Rule 3:
  Action: ALLOW
  Protocol: TCP/UDP
  Source IP: ANY
  Destination IP: ANY
  Port: 80, 443 (HTTP/HTTPS)
  Purpose: Allow web browsing

Default: DENY ALL (implicit)
```

> 💡 **Real-world analogy:** L3 Firewall Profile is like a **building access card system** at each door (AP) — instead of having one central security desk (FortiGate) that everyone must pass through, each door has its own card reader that enforces who can enter which area. Fast, local, but less sophisticated than central security.

---

---

# 📄 PAGE 267 — QoS Profile

---

## 🖥️ SLIDE SUMMARY

The slide explains **QoS Profiles** for wireless traffic:

**Traffic classification categories:**

- 🎙️ **Voice** — highest priority
- 📹 **Video** — high priority
- 📊 **Data** — normal priority
- 👥 **Users** — can set per-user limits

**Key capabilities:**

- Set **minimum and maximum bandwidth** per category
- Configure **WMM markings to DSCP value** translations
- Apply QoS profile in **SSID settings**

**WMM to DSCP translation:**

- WMM (Wi-Fi Multimedia) → 4 access categories: AC_VO, AC_VI, AC_BE, AC_BK
- FortiAP translates WMM priority → DSCP values when forwarding to Ethernet

**GUI Path:** `WiFi & Switch Controller > Operation Profiles > QoS Profiles`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Wireless QoS Is Critical

**The shared medium problem:**

```
Without QoS:
  Wi-Fi is a shared medium — all traffic competes equally for airtime
  
  VoIP call (100 Kbps, latency-sensitive) competes with:
  File download (10 Mbps, not latency-sensitive)
  
  Result: File download wins (more packets) → VoIP packet delayed
  → VoIP call breaks up → "choppy audio" → unusable
  
With QoS:
  VoIP packets marked as highest priority (AC_VO)
  File download marked as background (AC_BK)
  
  VoIP gets first access to channel → clear audio
  File download gets remaining airtime → slightly slower but acceptable
```

---

### WMM — Wi-Fi Multimedia Standard

**WMM (Wi-Fi Multimedia)** is the 802.11 QoS standard — defines 4 **Access Categories (AC)**:

| Access Category | Name        | Priority      | Use Case                   |
|-----------------|-------------|---------------|----------------------------|
| **AC_VO**       | Voice       | Highest (7-6) | VoIP, video conferencing   |
| **AC_VI**       | Video       | High (5-4)    | Video streaming            |
| **AC_BE**       | Best Effort | Normal (3-0)  | General web browsing, data |
| **AC_BK**       | Background  | Lowest (-1)   | File downloads, backups    |

**How WMM priority works:**

- Higher priority categories get **shorter TXOP (Transmission Opportunity) waiting times**
- Lower priority categories wait longer before transmitting
- Not absolute — higher priority just gets statistically better access, not guaranteed airtime

---

### DSCP — The Wired Network QoS Standard

**DSCP (Differentiated Services Code Point)** is the IP-level QoS marking used in wired networks:

```
IP packet header contains 6-bit DSCP field → 64 possible values

Common DSCP values:
  DSCP 46 (EF — Expedited Forwarding) = VoIP (highest priority)
  DSCP 34 (AF41) = Video streaming
  DSCP 0 (BE — Best Effort) = General data
  DSCP 8 (CS1) = Background (lowest priority)
```

---

### WMM to DSCP Translation — The Critical Function

**The translation problem:**

```
Wireless side: Client marks VoIP packet as AC_VO (WMM marking in 802.11 frame)
FortiAP receives packet → strips 802.11 header → creates Ethernet frame for wired network
Ethernet frame has no WMM field → QoS marking LOST without translation

With FortiAP WMM→DSCP translation:
  Client sends: 802.11 frame with AC_VO marking
  FortiAP converts: AC_VO → DSCP 46 (EF)
  FortiAP sends: Ethernet frame with DSCP 46 in IP header
  Wired switches/routers: See DSCP 46 → treat as highest priority
  → QoS preserved end-to-end (wireless + wired)
```

**WMM to DSCP mapping (typical):**

```
AC_VO → DSCP 46 (EF) or DSCP 48 (CS6)
AC_VI → DSCP 34 (AF41) or DSCP 32 (CS4)
AC_BE → DSCP 0 (BE)
AC_BK → DSCP 8 (CS1)
```

> 💡 **Real-world analogy:** WMM-to-DSCP translation is like translating a priority label from one postal system (wireless) to another (wired). A package marked "URGENT" on the UPS label must be re-labeled "EXPRESS" when it transfers to FedEx — same priority, different label format. FortiAP does this translation automatically.

---

### Bandwidth Limits Per Category

QoS Profile allows setting **per-SSID bandwidth limits**:

```
Guest SSID QoS Profile:
  Maximum bandwidth per user: 5 Mbps
  → Prevents one guest from monopolizing all bandwidth
  → Fair sharing among all guests

Corporate SSID QoS Profile:
  Voice minimum: 100 Kbps (guaranteed)
  Video minimum: 2 Mbps (guaranteed)
  Data: Best effort (remaining bandwidth)
  → VoIP always has enough bandwidth regardless of data traffic
```

---

---

# 📄 PAGE 268 — FortiAP Configuration Profiles

---

## 🖥️ SLIDE SUMMARY

The slide explains **FortiAP Configuration Profiles** — a way to configure local FortiAP settings:

**What it does:**

- Defines **local configuration settings** directly on FortiAP
- Uses a **command list** — same commands you'd type on FortiAP's local CLI
- Platform-specific — each profile tied to a **FortiAP family platform**

**Supported platforms:**

- FortiAP
- FortiAP-U
- FortiAP-C

**Use cases:**

- Set **discovery type** (AC_DISCOVERY_TYPE)
- Set **controller IP** (AC_IPADDR_1)
- Any other local FortiAP configuration command

**Important notes:**

- If FortiAP has a password set → must define it in the profile
- CLI reference: `config wireless-controller apcfg-profile`

**GUI Path:** `WiFi & Switch Controller > Operation Profiles > FortiAP Configuration Profiles`

---

## 📋 DETAILED INSTRUCTOR NOTES

### FortiAP Config Profile vs. AP Profile — Key Distinction

This is commonly confused:

| Profile Type                 | Controls                                                | Applied To                           |
|------------------------------|---------------------------------------------------------|--------------------------------------|
| **AP Profile (WTP Profile)** | Radio settings, SSIDs, TX power, channels               | FortiAP radios and wireless function |
| **FortiAP Config Profile**   | LOCAL AP settings (discovery, controller IP, passwords) | FortiAP's own local configuration    |

Think of it this way:

- **AP Profile** = what the AP broadcasts and how it operates wirelessly
- **FortiAP Config Profile** = the AP's own local settings (how it finds its controller, what password it uses)

---

### Why FortiAP Config Profiles Are Needed

**Scenario: Large deployment with 200 APs**

```
Problem: Each AP needs to know the controller IP address (AC_IPADDR_1)
         Each AP needs a local admin password set

Without config profiles:
  → Must SSH into each of 200 APs individually
  → Enter cfg -a AC_IPADDR_1=192.168.1.1 on each one
  → Set password on each one
  → 200 individual operations = hours of work

With FortiAP Config Profile:
  → Create one profile with: AC_IPADDR_1=192.168.1.1 + password
  → Assign profile to all 200 APs in Managed FortiAPs settings
  → FortiGate pushes config to all 200 APs simultaneously
  → Done in minutes
```

---

### Platform Specificity — The Same-Family Rule

The slide notes each profile is for a **specific FortiAP family platform**:

```
FortiAP 231F and FortiAP 233F → same platform family
  → One FortiAP Config Profile covers both models ✅

FortiAP 431F → different platform family
  → Needs its own FortiAP Config Profile ❌ (cannot share with 231F)
```

**Why platform-specific?**

- Different FortiAP models support different local configuration commands
- Commands available on FortiAP-231F may not exist on FortiAP-U-series
- Platform binding ensures commands in the profile are valid for the target hardware

---

### Common Commands in FortiAP Config Profiles

```bash
# Controller discovery settings
cfg -a AC_DISCOVERY_TYPE=0    # 0=auto, 1=static, 2=DHCP, 3=DNS
cfg -a AC_IPADDR_1=10.0.0.1   # Primary controller IP
cfg -a AC_IPADDR_2=10.0.0.2   # Secondary controller IP (failover)

# Mesh configuration
cfg -a MESH_AP_TYPE=1          # Enable mesh mode
cfg -a MESH_AP_SSID=MeshUplink # Backhaul SSID name

# Local password
cfg -a ADMIN_PASSWD=SecurePass123!  # Local admin password
```

---

---

# 📄 PAGE 269 — Guest Access Overview

---

## 🖥️ SLIDE SUMMARY

The slide introduces the **four methods** FortiGate provides for guest wireless access:

| Method                         | Description                                                    |
|--------------------------------|----------------------------------------------------------------|
| 🌐 **Guest SSID**              | Separate wireless network for guests — isolated from corporate |
| 📄 **Internal Captive Portal** | FortiGate hosts landing page before network access granted     |
| 👤 **Guest Management**        | Temporary guest account creation and distribution              |
| 🔗 **External Captive Portal** | Redirects guests to external URL for authentication/disclaimer |

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Guest Access Needs Careful Design

**The core security challenge:**

```
Without proper guest design:
  Guest connects to corporate Wi-Fi
  → Guest has access to corporate file servers, printers, internal systems
  → Guest can potentially attack internal devices
  → No audit trail of what guest accessed
  → Security incident: impossible to trace
  
With proper guest design:
  Guest connects to isolated guest SSID
  → Guest can ONLY access internet
  → Corporate resources completely isolated
  → Audit trail: who connected when, what they accessed
  → Security incident: fully traceable and containable
```

---

### The Four Methods — When to Use Each

**Method 1 — Guest SSID (Always Required):**

- Foundation of guest access — always deploy a **separate SSID** for guests
- Never put guests on the corporate SSID

**Method 2 — Internal Captive Portal:**

- Best when: Simple, FortiGate-hosted login/disclaimer page is sufficient
- No external server required
- Fast to deploy, lower complexity

**Method 3 — Guest Management:**

- Best when: Receptionists need to create temporary accounts for visiting clients
- Accounts expire automatically after defined time
- Accounts can be printed/emailed to guests

**Method 4 — External Captive Portal (FortiAuthenticator):**

- Best when: Advanced authentication needed (social login, SMS-OTP, LDAP, custom branding)
- More complex but much more feature-rich
- Integrates with existing identity infrastructure (Active Directory)

---

---

# 📄 PAGE 270 — Guest SSID

---

## 🖥️ SLIDE SUMMARY

The slide details **Guest SSID** configuration best practices:

**Key design principles:**

| Principle               | Detail                                                              |
|-------------------------|---------------------------------------------------------------------|
| **Separate SSID**       | Deploy dedicated guest wireless network without additional hardware |
| **Tunnel Mode**         | Use tunnel SSID for guests — ensures all traffic sent to FortiGate  |
| **Full control**        | FortiGate applies security profiles, firewall policies              |
| **Captive portal**      | Can use internal or external captive portal                         |
| **Selective broadcast** | Apply guest SSID only to APs where guests are expected              |

**Warning from slide:** Excessive SSIDs impact wireless performance — plan carefully

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Tunnel Mode Is Mandatory for Guest SSID

The slide explicitly recommends: *"You should use tunnel SSIDs to deploy guest network."*

**Critical reasoning:**

```
Bridge Mode guest SSID:
  Guest traffic exits locally at AP → FortiGate never sees it
  → Cannot apply web filter → guest accesses malware sites → infects device
  → Cannot apply IPS → guest launches attacks through your network
  → No visibility into what guests access → compliance failure
  → ISP may block your connection if guests download illegal content
  
Tunnel Mode guest SSID:
  All guest traffic → CAPWAP tunnel → FortiGate → inspection → internet
  → Web filter: block malware, illegal content, adult content
  → IPS: block attack tools, exploit attempts
  → Application control: block torrent clients
  → Full logging: complete audit trail per user
  → Your network protected from guest misuse
```

> 💡 **The compliance argument:** If a guest uses your network to download illegal content and you have no controls or logs, your organization may bear responsibility. Tunnel Mode + logging provides the audit trail needed for legal protection.

---

### Selective AP Broadcasting — Guest SSID Placement

The slide notes: *"Control where guest SSID is available — apply guest SSID to APs only where you expect guests."*

```
Smart guest SSID placement:
  ✅ Reception/lobby APs → broadcast guest SSID
  ✅ Conference room APs → broadcast guest SSID
  ✅ Public areas → broadcast guest SSID
  
  ❌ Server room APs → NO guest SSID (no guests allowed there)
  ❌ Executive floor APs → NO guest SSID
  ❌ Warehouse APs → NO guest SSID
  
Benefits:
  → Reduces SSID count on APs where not needed → better performance
  → Reduces attack surface → rogue guest devices cannot connect in secure areas
  → Cleaner RF environment in sensitive areas
```

---

---

# 📄 PAGE 271 — Captive Portal

---

## 🖥️ SLIDE SUMMARY

The slide explains **captive portals** — the landing page mechanism:

**What a captive portal does:**

- Used as a **landing page** after user connects to network
- Displays: **Disclaimer, Authentication page, Terms of use**
- FortiGate grants access **only after** user accepts disclaimer OR successfully authenticates
- Can be applied to **wired OR wireless interfaces**

**How it works:**

- Any HTTP request from unauthenticated user → **redirected to captive portal page**
- After successful interaction → user can access original URL and other resources
- Optionally: restrict access to **specific user group members only**

---

## �� DETAILED INSTRUCTOR NOTES

### The Captive Portal Mechanism — Technical Operation

**Phase 1 — Pre-authentication (all traffic blocked except captive portal):**

```
Guest connects to SSID → gets DHCP IP address
Guest opens browser → types www.google.com
FortiGate intercepts DNS request → resolves normally
FortiGate intercepts HTTP GET to google.com
FortiGate responds with HTTP 302 REDIRECT to captive portal URL
Guest browser → captive portal page displayed
```

**Phase 2 — During captive portal interaction:**

```
Guest sees: "Welcome to CompanyXYZ Guest WiFi"
Guest reads: Terms of service / disclaimer
Guest action: Clicks "Accept" OR enters username/password
Browser submits: Form data to FortiGate (or external portal)
```

**Phase 3 — Post-authentication (traffic allowed):**

```
FortiGate validates credentials (or confirms disclaimer accepted)
FortiGate creates: Authenticated session for this MAC address
FortiGate marks: Guest IP as "authenticated" in session table
Guest browser: Redirected to originally requested URL (www.google.com)
Guest: Normal internet browsing allowed (subject to security profiles)
```

---

### Captive Portal on Wired Interfaces

Important note from slide: *"Can be applied to a wired or wireless interface."*

This means guest conference rooms with **Ethernet ports** can also require captive portal authentication:

```
Wired Ethernet port in conference room:
  Visitor plugs in laptop → gets DHCP
  Opens browser → captive portal page
  Authenticates → gets access
  → Same experience as wireless captive portal
  → Same security controls apply
```

---

---

# 📄 PAGE 272 — Captive Portal Types

---

## 🖥️ SLIDE SUMMARY

The slide lists **all four captive portal types**:

| Type                            | What User Must Do                  | Notes                 |
|---------------------------------|------------------------------------|-----------------------|
| **Authentication**              | Provide username + password        | Standard login        |
| **Disclaimer + Authentication** | Accept disclaimer AND authenticate | Highest assurance     |
| **Disclaimer Only**             | Accept disclaimer (no login)       | Lowest friction       |
| **Email Collection**            | Enter email address                | Collects contact info |

**Email Collection additional detail:**

- Emails collected stored at: `Dashboard > Users & Devices > Collected Email`
- Must enable **Email Collection in Feature Visibility** first

**GUI Path:** `WiFi & Switch Controller > SSIDs`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Email Collection — The Fourth Type (Often Overlooked)

The slide mentions a **fourth captive portal type** not covered previously — **Email Collection**:

```
Email Collection captive portal:
  User connects → captive portal shows
  Portal displays: disclaimer + mandatory email address field
  User MUST enter email to gain access
  → No password required (unlike Authentication type)
  → Email is the "payment" for access
  
Use cases:
  Marketing: Build email list while providing guest Wi-Fi
  Events: Collect attendee contact information
  Hotels: Collect guest email for future marketing
  Restaurants: Build customer loyalty program list
  
Where emails go:
  Dashboard > Users & Devices > Collected Email
  Can be exported to CRM/marketing systems
```

**Feature Visibility requirement:**

- Email Collection portal type only appears after enabling it in `System > Feature Visibility`
- Hidden by default — must be explicitly enabled

---

### Choosing the Right Portal Type

```
Decision framework:

Is identity tracking required?
  YES → Authentication or Disclaimer + Authentication
  NO → Disclaimer Only or Email Collection

Is legal protection (disclaimer) required?
  YES → Disclaimer Only / Disclaimer + Auth / Email Collection
  NO → Authentication only

Is marketing data valuable?
  YES → Email Collection (get emails) or Social (get demographics)
  NO → Disclaimer Only

Is this a high-security environment?
  YES → Disclaimer + Authentication (maximum assurance)
  NO → Disclaimer Only (minimum friction)
```

---

---

# 📄 PAGE 273 — Captive Portal Authentication Types

---

## 🖥️ SLIDE SUMMARY

The slide covers the **two authentication portal options**:

| Type         | Who Hosts the Login Page                     | Who Processes Authentication           |
|--------------|----------------------------------------------|----------------------------------------|
| **Local**    | FortiGate (built-in portal page)             | FortiGate (against local DB or RADIUS) |
| **External** | External server (FortiAuthenticator, custom) | External server                        |

**Configuration fields shown in GUI:**

- **Challenge unauthenticated user's method** — HTTP or HTTPS challenge
- **Where captive portal is located** — Local or External
- **Authenticated users must be part of specified group** — group-based access control
- **External captive portal server addresses** — FQDN or IP of external server

**GUI Path:** `WiFi & Switch Controller > SSIDs`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Local vs. External — Detailed Comparison

**Local Captive Portal:**

```
All hosted on FortiGate:
  Login page HTML: stored in FortiGate replacement messages
  User authentication: FortiGate validates against local users OR RADIUS
  Session management: FortiGate tracks authenticated sessions
  
Configuration:
  Simple — no external server needed
  Customize via: System > Replacement Messages > WiFi
  
Limitations:
  Basic customization only (HTML templates)
  No social login
  No SMS-OTP
  No advanced identity features
  
Best for: Simple environments, small deployments, basic security
```

**External Captive Portal (FortiAuthenticator):**

```
Login page HTML: hosted on FortiAuthenticator (full web server)
User authentication: FortiAuthenticator validates (supports LDAP/AD/RADIUS/OTP/social)
Session management: FortiGate tracks, FortiAuthenticator authorizes

Configuration:
  More complex — FortiAuthenticator server required
  Full HTML/CSS customization
  Company branding, logos, colors
  
Capabilities:
  Social login (Facebook, Google, LinkedIn)
  SMS-OTP (text message verification)
  Email OTP
  LDAP/Active Directory integration
  Self-registration portal
  
Best for: Enterprise, rich feature set, custom branding requirements
```

---

### The External Server Field — FQDN vs. IP

The slide shows an **External captive portal server addresses** field.

**Important:** Use **FQDN** (not IP address) when HTTPS is enforced:

```
Why FQDN for HTTPS?
  SSL/TLS certificate validation checks:
    "Does the CN (Common Name) on the certificate match the URL I'm accessing?"
    
  If using IP: https://192.168.1.100
    → Certificate has CN = "fac.company.com"
    → Browser: "Certificate doesn't match IP" → CERTIFICATE WARNING
    
  If using FQDN: https://fac.company.com
    → Certificate has CN = "fac.company.com"
    → Browser: "Certificate matches" → No warning
    → Clean, professional guest experience
```

---

---

# 📄 PAGE 274 — Captive Portal — Exempt Destinations/Services

---

## 🖥️ SLIDE SUMMARY

The slide explains **traffic exemptions** from captive portal:

**The default behavior:**

- FortiGate **blocks ALL traffic** from unauthenticated captive portal users
- ALL HTTP redirected to captive portal page
- Non-HTTP traffic: BLOCKED

**Why exemptions are needed:**

- External captive portal server itself must be reachable BEFORE authentication
- DNS must work to resolve the external portal URL
- Some devices cannot interact with captive portals (printers, IoT)

**Two-step exemption process:**

```
Step 1: Add destination to Exempt Destinations/Services in SSID settings
Step 2: Create corresponding firewall policy to allow the traffic
```

> ⚠️ **Critical:** Step 1 alone is NOT enough — firewall policy (Step 2) is ALSO required

**GUI Path:** `WiFi & Switch Controller > SSIDs`

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Bootstrap Problem — Why Exemptions Are Essential

**The chicken-and-egg problem with external captive portals:**

```
User tries to access internet → redirected to captive portal
Captive portal is at: https://fac.company.com
FortiGate must redirect user to fac.company.com

But FortiGate is blocking ALL traffic before authentication...
...including traffic to fac.company.com!

Result without exemption:
  User redirected to fac.company.com → connection refused
  Cannot reach the captive portal page → cannot authenticate
  → Permanent deadlock: cannot authenticate because cannot reach authentication page
```

**Solution — Exempt the captive portal server:**

```
Exempt Destinations/Services in SSID:
  Add: fac.company.com (address object)
  Add: DNS (service)
  
Firewall Policy (pinhole):
  Source: [SSID interface]
  Destination: fac.company.com
  Service: HTTPS (TCP 443)
  Action: ACCEPT
  ← NO user group restriction → allows unauthenticated users
  
Result:
  Unauthenticated user → FortiGate blocks most traffic
  But ALLOWS traffic to fac.company.com → portal page loads
  User authenticates → FortiGate unlocks general internet access
```

---

### Source IP Exemptions — IoT and Non-Browser Devices

The slide also mentions **source IP exemptions**:

```
Problem: Some devices cannot interact with captive portals
  Examples:
    Printers → need internet for firmware updates
    IoT sensors → need cloud connectivity
    Network cameras → need NTP, firmware update servers
    
These devices:
  → Cannot open a browser
  → Cannot accept a disclaimer
  → Cannot enter credentials
  → Get stuck: FortiGate blocks all their traffic

Solution: Exempt by SOURCE IP
  Add printer's IP (10.10.2.50) as exempt source
  Printer gets internet access without captive portal challenge
  → Prints, updates firmware, works normally
```

---

### Two-Step Process — Why Both Steps Are Required

```
Step 1 only (SSID exemption but no firewall policy):
  FortiGate marks the traffic as exempt from captive portal challenge
  BUT: No firewall policy exists to ALLOW the traffic
  → FortiGate still DROPS the packets (default deny)
  → Exemption alone does nothing

Step 2 only (firewall policy but no SSID exemption):
  Firewall policy allows the traffic
  BUT: FortiGate still challenges unauthenticated users with captive portal
  → User still gets redirected to portal page
  → Never reaches the actual destination

Both steps required:
  Step 1: "Don't challenge this traffic with captive portal"
  Step 2: "Allow this traffic through the firewall"
  → Combined: Traffic reaches destination without captive portal challenge
```

> ⚠️ **Trainer Warning:** This two-step requirement catches many administrators. It's one of the most common captive portal troubleshooting issues — exempt destination added in SSID settings but firewall policy forgotten. Always verify BOTH steps when troubleshooting captive portal exemptions.

---

---

# 📄 PAGE 275 — Captive Portal — Firewall Policy Method

---

## 🖥️ SLIDE SUMMARY

The slide presents an **alternative method** to exempt captive portal traffic:

**Alternative approach:**

- Create a **firewall policy** instead of using Exempt Destinations in SSID settings
- Enable **"Exempt from Captive Portal"** option on the firewall policy itself

**Important notes:**

- Advanced option — must enable **policy advanced options** in `System > Feature Visibility` to see it
- If HTTPS is enforced → portal address must be **FQDN** (not IP)
- FQDN must resolve to the FortiGate interface IP where captive portal is enabled

**GUI Path:** `Policy & Objects > Firewall Policy`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Method 1 vs. Method 2 — Comparison

**Method 1 — Exempt Destinations in SSID Settings (Page 274):**

```
Where configured: SSID interface settings (WiFi & Switch Controller > SSIDs)
How it works: Two steps — SSID exemption + separate firewall policy
Visibility: Basic feature visibility setting
Use case: Simple exemptions, per-SSID control
```

**Method 2 — Exempt from Captive Portal in Firewall Policy (Page 275):**

```
Where configured: Firewall Policy (Policy & Objects > Firewall Policy)
How it works: One object — firewall policy with exempt flag
Visibility: Requires advanced options enabled in Feature Visibility
Use case: Complex deployments, more granular control, aligns with standard firewall policy management
```

**When to use Method 2:**

- Large deployments where all firewall policies are managed centrally
- Organizations that prefer single-object management (one policy = one rule)
- When Method 1's two-step process creates management complexity

---

### The FQDN Requirement for HTTPS — Critical Detail

```
Scenario: FortiGate interface IP = 10.0.3.254
          Captive portal enabled on this interface
          External portal server = FortiAuthenticator at fac.company.com

Configure in auth-portal settings:
  config firewall auth-portal
    set identity-based-route (name)
    set portal-addr "fac.company.com"  # Use FQDN
  end
  
Why FQDN and not IP?
  HTTPS enforced: Client browser validates SSL certificate
  Certificate CN = "fac.company.com"
  If redirect URL = https://10.0.3.254:1003 → CN mismatch → browser warning
  If redirect URL = https://fac.company.com:1003 → CN match → no warning
  
DNS requirement:
  "fac.company.com" must resolve to 10.0.3.254 (FortiGate interface)
  → DNS A record: fac.company.com → 10.0.3.254
```

---

---

# 📄 PAGE 276 — Firewall Policy for Authenticated Guests

---

## 🖥️ SLIDE SUMMARY

The slide shows the **firewall policy for authenticated guest access**:

**Policy design:**

| Field                          | Value                |
|--------------------------------|----------------------|
| **Source Interface**           | Guest SSID interface |
| **Destination Interface**      | WAN (internet)       |
| **Source User Group**          | Guest user group     |
| **Action**                     | ACCEPT               |
| **Exempt from Captive Portal** | ❌ **DO NOT ENABLE**  |

**Key warning:**

> Do NOT enable "Exempt from Captive Portal" on the main guest internet policy — otherwise ALL traffic bypasses the captive portal completely.

**GUI Path:** `Policy & Objects > Firewall Policy`

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Two-Policy Architecture — Complete Guest Access Design

A complete guest access deployment requires **exactly two policies**:

**Policy 1 — Exempt Policy (for captive portal server traffic):**

```
Name: Guest-Portal-Exempt
Source Interface: Guest-SSID
Destination Interface: wan1
Destination: fac.company.com (captive portal server)
Service: DNS, HTTPS
Action: ACCEPT
Exempt from Captive Portal: ✅ ENABLED
User Group: [none specified — allows unauthenticated]
Purpose: Allows unauthenticated guests to reach captive portal
```

**Policy 2 — Authenticated Internet Policy:**

```
Name: Guest-Internet-Access
Source Interface: Guest-SSID
Destination Interface: wan1
Destination: all
Service: HTTP, HTTPS, DNS
Action: ACCEPT
Exempt from Captive Portal: ❌ DISABLED (CRITICAL!)
User Group: "Guest-Users" (REQUIRED)
Security Profiles: Web Filter, Application Control, Antivirus
Purpose: Allows AUTHENTICATED guests to reach internet
         Blocks non-authenticated guests (they haven't completed captive portal)
```

---

### The Critical Danger of "Exempt from Captive Portal" on Policy 2

```
WRONG CONFIGURATION (common mistake):
  Policy 2 has "Exempt from Captive Portal" = ENABLED
  
  Effect:
    ALL guest traffic bypasses captive portal completely
    No one ever sees the portal page
    No one accepts disclaimer → legal exposure
    No one authenticates → no identity tracking
    Anyone can connect and get full internet access
    → Complete security and compliance failure
    
CORRECT CONFIGURATION:
  Policy 2 has "Exempt from Captive Portal" = DISABLED (default)
  AND User Group = "Guest-Users" specified
  
  Effect:
    Unauthenticated users → captive portal page (Policy 2 matches but Group doesn't → redirected)
    Authenticated users in "Guest-Users" group → internet access granted
    → Correct captive portal enforcement
```

---

---

# 📄 PAGE 277 — External Captive Portal Workflow

---

## 🖥️ SLIDE SUMMARY

The slide shows the **complete 12-step external captive portal workflow** with FortiAuthenticator:

```
Step 1:  Client requests internet page
Step 2:  FortiGate redirects to FortiAuthenticator captive portal page
Step 3:  FortiAuthenticator presents login page
Step 4:  User submits login credentials
Step 5:  FortiAuthenticator instructs browser to POST credentials to FortiGate
Step 6:  Client POSTs login credentials to FortiGate (HTTPS POST)
Step 7:  FortiGate sends RADIUS Access-Request to FortiAuthenticator
Step 8:  FortiAuthenticator sends RADIUS Access-Accept (or Reject)
Step 9:  FortiGate sends RADIUS Accounting-Request to FortiAuthenticator
Step 10: FortiAuthenticator sends Accounting-Response
Step 11: FortiAuthenticator redirects user to original URL
Step 12: Client allowed access to the internet
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### The 12-Step Workflow — Deep Technical Analysis

**Steps 1-3 — The Redirect Phase:**

```
Client: GET http://www.google.com HTTP/1.1
FortiGate: HTTP 302 REDIRECT → https://fac.company.com/guests/login/?magic=xxx&post=xxx...
Client: GET https://fac.company.com/guests/login/?magic=xxx
FortiAuthenticator: Returns login page HTML
Client: Renders login page in browser
```

**Steps 4-6 — The Credential Submission Phase (Critical Design):**

```
User enters credentials on FortiAuthenticator page
FortiAuthenticator HTML form action: POSTS to FortiGate (not back to FortiAuthenticator!)

Why POST to FortiGate?
  FortiGate controls the session/policy
  FortiGate must be the one to authenticate the user (via RADIUS)
  FortiAuthenticator cannot directly instruct FortiGate to allow traffic
  → Solution: FortiAuthenticator instructs client's browser to POST to FortiGate
  → FortiGate receives credentials → processes authentication itself
```

**Steps 7-8 — RADIUS Authentication:**

```
FortiGate → FortiAuthenticator (RADIUS):
  Access-Request:
    User-Name: john.doe
    User-Password: [encrypted]
    NAS-IP-Address: [FortiGate IP]

FortiAuthenticator validates:
  Check local DB or LDAP/AD
  Valid user? → Access-Accept
    Session attributes:
      Session-Timeout: 3600 (1 hour session limit)
      Bandwidth-Max: 10 Mbps
      WISPr-Bandwidth-Max-Up: 5 Mbps
  Invalid user? → Access-Reject → User sees "Login failed"
```

**Steps 9-10 — RADIUS Accounting:**

```
FortiGate → FortiAuthenticator (RADIUS Accounting):
  Accounting-Request: Start
    Acct-Session-Id: unique session identifier
    User-Name: john.doe
    Framed-IP-Address: 10.0.3.1 (guest's IP)
    NAS-IP-Address: [FortiGate IP]
    Timestamp: [current time]
    
Purpose:
  Verify no duplicate session exists
  Begin accounting record (for session duration tracking, billing, compliance)
  FortiAuthenticator maintains complete session audit log
```

**Steps 11-12 — Access Granted:**

```
FortiAuthenticator → browser: HTTP redirect to original URL (www.google.com)
FortiGate: Session marked as authenticated for this MAC/IP
Client: Successfully loads www.google.com
Session timer starts: 1 hour countdown (or configured timeout)
```

---

---

# 📄 PAGE 278 — HTTPS POST

---

## 🖥️ SLIDE SUMMARY

The slide explains **HTTPS POST** for captive portal credential security:

**What it does:**

- Uses **HTTPS instead of HTTP** for user credential submission
- Credentials protected by **SSL tunnel** — cannot be intercepted in plaintext

**Configuration:**

- Configurable via CLI only
- Default port: **1003**
- Change port: `config system settings → set auth-https-port {integer}`

**GUI Path:** `User & Authentication > Authentication Settings`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why HTTPS POST Matters — Credential Security

**HTTP POST (no encryption):**

```
User enters: username=john, password=Secret123
Browser submits: POST http://10.0.3.254:1000/fgtauth
  Body: username=john&password=Secret123
  
Network packet:
  [IP Header][TCP Header][HTTP Headers][username=john&password=Secret123]
  → Visible in plaintext to anyone sniffing the network
  → Man-in-the-middle can steal credentials
  → Completely insecure for credential submission
```

**HTTPS POST (encrypted):**

```
User enters: username=john, password=Secret123
Browser submits: POST https://10.0.3.254:1003/fgtauth (encrypted with TLS)
  
Network packet:
  [IP Header][TCP Header][TLS Encrypted Data → username=john&password=Secret123]
  → Credentials encrypted → cannot be read by sniffers
  → Man-in-the-middle cannot steal credentials
  → Secure credential transmission
```

---

### Port 1003 — Why a Non-Standard Port?

Standard HTTPS uses port 443. FortiGate uses **port 1003** for captive portal HTTPS authentication:

- Port 443 already in use by FortiGate's management HTTPS interface
- Port 1003 is the **dedicated authentication port** — separate from management traffic
- Clear traffic separation: 443 = admin management, 1003 = captive portal auth

**Changing the port (when needed):**

```bash
config system settings
    set auth-https-port 8443    # Change to 8443 if 1003 is blocked by firewall
end
```

> ⚠️ **Note:** If guest network traffic passes through a restrictive upstream firewall, port 1003 may be blocked. In that case, change to a commonly allowed port (443 or 8443).

---

---

# 📄 PAGE 279 — Authentication Certificate

---

## 🖥️ SLIDE SUMMARY

The slide explains **captive portal certificate management**:

**Default behavior:**

- FortiGate presents its **factory default self-signed certificate** on the authentication HTTPS page
- Self-signed certificate → **browser security warning** for guests

**Two different certificates:**

| Certificate                    | Purpose                                                 |
|--------------------------------|---------------------------------------------------------|
| **Authentication certificate** | Secures the captive portal webpage (HTTPS)              |
| **Wi-Fi certificate**          | Identifies controller as authentication server (802.1X) |

**Solution:**

- Upload a **publicly signed certificate** to FortiGate
- Specify it in `User & Authentication > Authentication Settings`
- Browser trusts it → **no certificate warning** for guests

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Self-Signed Certificate Problem

**What guests experience with default factory certificate:**

```
Guest opens browser → FortiGate redirects to HTTPS captive portal
Browser checks certificate:
  "Is this certificate signed by a trusted CA?"
  Factory cert: Self-signed by FortiGate → NOT trusted
  
Browser shows warning:
  "Your connection is not private"
  "Attackers might be trying to steal your information"
  "NET::ERR_CERT_AUTHORITY_INVALID"
  
Guest reaction:
  "This Wi-Fi looks dangerous — I'm not using it"
  OR
  "I have to click through a scary warning just to use guest Wi-Fi"
  → Poor user experience → brand damage
```

**Solution — publicly signed certificate:**

```
Administrator:
  1. Purchase SSL certificate from trusted CA (DigiCert, Let's Encrypt, etc.)
     OR use internal CA if guests' devices trust it
  2. Upload certificate to FortiGate: System > Certificates
  3. Select certificate in: User & Authentication > Authentication Settings
  
Guest experience with trusted certificate:
  Browser: "Connection secure" (padlock icon)
  No warning displayed
  Clean, professional captive portal experience
```

---

### The Two Certificates — Don't Confuse Them

```
Certificate 1 — Authentication/Captive Portal Certificate:
  Purpose: HTTPS security for the captive portal web page
  Used by: Guest browsers connecting to captive portal
  Location: User & Authentication > Authentication Settings
  Type: Standard SSL/TLS web certificate
  CN: Must match the portal URL (e.g., portal.company.com)

Certificate 2 — Wi-Fi Certificate (802.1X):
  Purpose: FortiGate identifies itself as RADIUS/EAP authentication server
  Used by: 802.1X supplicants (laptops, phones doing EAP-PEAP)
  Location: WiFi & Switch Controller > WiFi Settings
  Type: TLS server certificate for EAP authentication
  CN: Must match what supplicants expect
```

Mixing these up is a common source of wireless authentication failures. Always verify which certificate context you're working in.

---

---

# 📄 PAGE 280 — Captive Portal POST Parameters

---

## 🖥️ SLIDE SUMMARY

The slide shows the **URL parameters FortiGate sends** when redirecting to the external captive portal:

**Example redirect URL:**

```
https://fac.trainingad.training.lab/guests/login/?login
  &post=https://auth.trainingad.training.lab:1003/fgtauth
  &magic=000a038293d1f411
  &usermac=b8:27:eb:d8:50:02
  &apmac=70:4c:a5:9d:0d:28
  &apip=10.10.100.2
  &userip=10.0.3.1
  &ssid=Guest03
  &apname=PS221ETF18000148
  &bssid=70:4c:a5:9d:0d:30
```

**Three key components:**

1. **External server address** — IP or FQDN of FortiAuthenticator (FQDN required for HTTPS)
2. **FortiGate IP/FQDN** — where credentials are POSTed back after authentication
3. **Magic parameter** — session ID tracking the request

---

## 📋 DETAILED INSTRUCTOR NOTES

### URL Parameter Breakdown — Complete Analysis

```
Parameter           Value                    Purpose
─────────────────────────────────────────────────────────────────────────
External server:    fac.trainingad.training.lab    FortiAuthenticator FQDN
/guests/login/      Path on FortiAuthenticator     Login page URL path

?login              Query parameter                Indicates login request

&post=              https://auth.../fgtauth        WHERE browser POSTs credentials
                                                   (FortiGate's address + /fgtauth path)

&magic=             000a038293d1f411               SESSION ID — unique per attempt
                                                   Prevents session mixing between users
                                                   Also called: magic token / session cookie

&usermac=           b8:27:eb:d8:50:02             GUEST'S device MAC address
                                                   FortiAuthenticator can track per device

&apmac=             70:4c:a5:9d:0d:28             AP's MAC address
                                                   Identifies which AP guest is using

&apip=              10.10.100.2                    AP's IP address
                                                   For location awareness

&userip=            10.0.3.1                       GUEST'S IP address
                                                   FortiAuthenticator can pre-populate IP

&ssid=              Guest03                        SSID name guest connected to
                                                   Portal can customize per SSID

&apname=            PS221ETF18000148               AP's configured name/serial
                                                   For location tracking in portals

&bssid=             70:4c:a5:9d:0d:30             AP's BSSID (radio MAC)
                                                   Different from apmac (device MAC)
```

---

### The Magic Parameter — Session Security

**Why the Magic parameter matters:**

```
Without magic parameter:
  Multiple guests all redirected to same portal URL
  Browser POSTs credentials → FortiGate: "Which session is this for?"
  Session mixing → Guest A's credentials authenticate Guest B's session
  → Security failure
  
With magic parameter:
  FortiGate generates unique magic token per redirect: 000a038293d1f411
  Token tied to: [Guest MAC] + [Guest IP] + [timestamp]
  When credentials POSTed back: magic token included
  FortiGate: "Magic 000a03... → this is Guest B's session" → correctly applies
  → Each authentication is uniquely and correctly tracked
```

---

---

# 📄 PAGE 281 — POST Parameters (Complete List)

---

## 🖥️ SLIDE SUMMARY

The slide provides the **complete list of all POST parameters** sent to external captive portal:

```
apname=P321CR3X16000103         → AP name/serial
apip=10.10.100.2                → AP IP address
ssid=Guest01                    → SSID name
bssid=90:6c:ac:3f:3e:28        → AP BSSID (radio MAC)
apmac=90:6c:ac:3f:3e:18        → AP hardware MAC
usermac=e4:a4:71:80:bc:27      → Guest device MAC
device_type=windows-pc          → Guest device OS/type
userip=10.0.3.1                 → Guest IP address
magic=06090a8f989d0517          → Session ID token
login=                          → Login action indicator
post=http://10.0.3.254:1000/fgtauth → Where to POST credentials
```

---

## 📋 DETAILED INSTRUCTOR NOTES

### Using POST Parameters in Custom Portals

**FortiAuthenticator and custom portal developers can use these parameters for:**

```
apname / apip / bssid:
  → Location awareness: "You're connecting from the 3rd floor conference room AP"
  → Location-specific portal content: different banners per location
  → Troubleshooting: track which AP is causing authentication issues

usermac:
  → Device-level policy: different access for known vs. unknown devices
  → Track returning devices even with guest accounts
  → MAC filtering integration

device_type:
  → Device-specific portal: mobile users see mobile-optimized portal
  → Policy differentiation: Windows PCs vs. phones get different bandwidth limits
  → Analytics: which device types connect most?

ssid:
  → Different portal pages per SSID: corporate guests vs. public guests
  → SSID-specific disclaimer text
  → Different session limits per SSID
```

**Custom portal development example:**

```python
# Custom portal reads POST parameters
def process_redirect(request):
    ap_name = request.GET.get('apname')
    device_type = request.GET.get('device_type')
    ssid = request.GET.get('ssid')
    
    if ssid == 'VIP-Guest':
        # Show premium portal with higher bandwidth
        return render_vip_portal(magic=request.GET.get('magic'))
    else:
        return render_standard_portal(magic=request.GET.get('magic'))
```

---

---

# 📄 PAGE 282 — FortiAP BLE Scanning

---

## 🖥️ SLIDE SUMMARY

The slide introduces **FortiAP BLE (Bluetooth Low Energy) Scanning**:

**What it does:**

- FortiAP models with **built-in Bluetooth radio** can scan for BLE beacons
- Provides: **location-based services**, peripheral information gathering
- Enhances **wireless users' experience on site**

**Where BLE beacons are used:**

- 🏛️ Museums (exhibit proximity notifications)
- 🛍️ Malls (store proximity alerts, promotions)
- 🏟️ Stadiums (seat location services, concession alerts)

**Configuration — CLI only:**

```bash
config wireless-controller ble-profile
    edit "fortiap-discovery"
        set ibeacon-uuid "wtp-uuid"    # Unique beacon identifier
        set major-id 1000              # Major location group ID
        set minor-id 2000              # Minor location sub-ID
        set txpower 0                  # BLE transmit power
        set beacon-interval 100        # Beacon broadcast interval
        set ble-scanning enable        # Enable BLE scanning
    next
end
```

**Assignment:** BLE profile assigned within **FortiAP profile**

---

## 📋 DETAILED INSTRUCTOR NOTES

### BLE Technology — Brief Primer

**Bluetooth Low Energy (BLE)** is a wireless technology designed for:

- Very **low power consumption** (coin battery can last years)
- Short to medium range (typically 10-100 meters)
- Periodic broadcasting of small data packets (beacons)

**Two BLE use cases in FortiAP:**

**Use Case 1 — BLE Scanning (RECEIVE mode):**

```
FortiAP radio listens for BLE beacon signals
Devices in the area broadcast BLE beacons (iBeacon, Eddystone format)
FortiAP collects: UUID, Major ID, Minor ID, RSSI of each beacon
Sends data to FortiGate for processing
→ Used for: Asset tracking, visitor location, customer proximity
```

**Use Case 2 — BLE Beaconing (TRANSMIT mode):**

```
FortiAP broadcasts its own BLE beacon signal
Visitor's phone app detects the beacon
App knows: which AP → floor plan location
→ Used for: Indoor navigation, proximity-based push notifications
```

---

### iBeacon Format — The Location Standard

**iBeacon** is Apple's BLE beacon format (widely supported):

```
iBeacon packet structure:
  UUID (128-bit): Company/venue identifier
    Example: "12345678-1234-1234-1234-123456789ABC"
    → Identifies this beacon network (your company)
    
  Major ID (16-bit, 0-65535): Location group
    Example: 1000 = "Downtown Mall, Building A"
    → Identifies a major location within the UUID namespace
    
  Minor ID (16-bit, 0-65535): Specific location
    Example: 2000 = "Ground Floor, Electronics Section"
    → Identifies a specific spot within the major location

Phone app reads: UUID → confirm it's your network
                 Major → know which building
                 Minor → know which floor/area
                 RSSI → estimate distance from beacon
```

---

### Pole Star and Eddystone Integration

The slide mentions additional resources **Pole Star and Eddystone**:

**Pole Star:**

- Indoor positioning system company
- FortiPresence/FortiAP can integrate with Pole Star's RTLS (Real-Time Location System)
- Provides cm-level positioning accuracy (much better than Wi-Fi triangulation)
- Uses combination of Wi-Fi RSSI + BLE beacon data

**Eddystone:**

- Google's open BLE beacon format (alternative to Apple's iBeacon)
- Three frame types:
  - Eddystone-URL: broadcasts a URL directly (phone can open without app)
  - Eddystone-UID: broadcasts a unique ID (similar to iBeacon Major/Minor)
  - Eddystone-TLM: broadcasts beacon telemetry (battery, temperature)

> 💡 **Real-world application:** A museum uses FortiAP BLE scanning. When a visitor stands near an exhibit, their phone app (connected to the guest Wi-Fi) receives a notification: "You're near the Mona Lisa exhibit — tap to learn more." The FortiAP detected the visitor's phone proximity via BLE and triggered the contextual notification. No separate beacon hardware needed — FortiAP handles both Wi-Fi and BLE.

---

---

# 📊 Master Summary — Pages 265–282

```
┌──────────────────────────────────────────────────────────────────────────┐
│     ADVANCED WIRELESS FEATURES & GUEST ACCESS — COMPLETE GUIDE           │
├─────────────────────────┬────────────────────────────────────────────────┤
│ TOPIC                   │ KEY FACTS                                      │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Advanced Features       │ Hidden by default → enable in Feature Visibility│
│                         │ Three new profiles: QoS, L3 Firewall, AP Config │
│                         │ QoS + L3 Firewall → assigned in SSID settings  │
│                         │ AP Config Profile → assigned in Managed FortiAPs│
├─────────────────────────┼────────────────────────────────────────────────┤
│ L3 Firewall Profile     │ Bridge access control list (ACL)               │
│                         │ Enforced at AP level (Bridge Mode primarily)   │
│                         │ Supports IPv4 and IPv6                         │
│                         │ Rules: IP + port + IANA protocol               │
├─────────────────────────┼────────────────────────────────────────────────┤
│ QoS Profile             │ Classification: Voice/Video/Data/Users         │
│                         │ Min/Max bandwidth per category                 │
│                         │ WMM → DSCP translation for wired QoS continuity│
│                         │ AC_VO=highest → AC_BK=lowest priority          │
├─────────────────────────┼────────────────────────────────────────────────┤
│ FortiAP Config Profile  │ Local AP settings via command list              │
│                         │ Platform-specific (same family = same profile)  │
│                         │ Common: AC_DISCOVERY_TYPE, AC_IPADDR_1          │
│                         │ Assigned in Managed FortiAPs settings           │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Guest Access Methods    │ 1. Guest SSID (always use Tunnel Mode)         │
│                         │ 2. Internal Captive Portal (FortiGate-hosted)  │
│                         │ 3. Guest Management (temp accounts)            │
│                         │ 4. External Captive Portal (FortiAuthenticator) │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Captive Portal Types    │ Authentication: username + password            │
│                         │ Disclaimer + Auth: accept terms + login        │
│                         │ Disclaimer Only: accept terms (no login)       │
│                         │ Email Collection: enter email (4th type)       │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Captive Portal Exemptions│ Two-step: SSID exemption + firewall policy    │
│                         │ ⚠️ BOTH steps required — neither alone works   │
│                         │ Alt: Firewall policy + "Exempt from CP" flag   │
│                         │ Source IP exemption for IoT/printers           │
├─────────────────────────┼────────────────────────────────────────────────┤
│ External CP Workflow    │ 12-step process (client → redirect → auth →    │
│                         │ RADIUS → accounting → access granted)          │
│                         │ Credentials POSTed to FortiGate (not ext server)│
│                         │ RADIUS Access-Request/Accept → Accounting       │
├─────────────────────────┼────────────────────────────────────────────────┤
│ HTTPS POST              │ Default port: 1003 (change with auth-https-port)│
│                         │ Protects credentials in SSL tunnel             │
│                         │ Use FQDN (not IP) when HTTPS enforced          │
├─────────────────────────┼────────────────────────────────────────────────┤
│ POST Parameters         │ magic = session ID (prevents session mixing)   │
│                         │ usermac, apname, apip, ssid, device_type, etc. │
│                         │ External portal can use these for customization │
├─────────────────────────┼────────────────────────────────────────────────┤
│ BLE Scanning            │ FortiAP models with BLE radio scan beacons     │
│                         │ iBeacon: UUID + Major ID + Minor ID            │
│                         │ Use: museums, malls, stadiums, asset tracking  │
│                         │ Config: CLI only (ble-profile)                 │
│                         │ Integrates with: Pole Star RTLS, Eddystone     │
└─────────────────────────┴────────────────────────────────────────────────┘

⚠️  CRITICAL RULES TO REMEMBER:
  1. Guest SSID MUST use Tunnel Mode for security and compliance
  2. Captive portal exemptions need TWO steps — SSID + firewall policy BOTH
  3. DO NOT enable "Exempt from Captive Portal" on the authenticated policy
  4. HTTPS captive portal: Use FQDN (not IP) for certificate validation
  5. Auth certificate ≠ Wi-Fi certificate — completely different purposes
  6. Default HTTPS auth port = 1003 (may need to change if blocked)
  7. Magic parameter = session ID — critical for session security
  8. WMM→DSCP translation needed to preserve QoS across wireless/wired boundary
  9. FortiAP Config Profile = local AP settings; AP Profile = radio settings
```

---

### Topic: Advanced Options and Monitoring — Wireless Monitoring Tools

---

---

# 📄 PAGE 286 — Managed FortiAPs Table

---

## 🖥️ SLIDE SUMMARY

The slide introduces the **Managed FortiAPs Table** — the primary AP monitoring interface:

**Location:** `WiFi & Switch Controller > Managed FortiAPs`

**Three available views:**

| View           | Best For                      | Key Feature                               |
|----------------|-------------------------------|-------------------------------------------|
| **AP View**    | Default — overview of all APs | Groups radio interfaces under each AP     |
| **Radio View** | Assessing radio load          | Sort radios by utilization — spot trouble |
| **Group View** | Organized deployments         | APs organized by logical groups           |

**Both AP and Radio views show:**

- **Radio utilization** (add via right-click column header → Channel Utilization)
- **Client count** per AP/radio

**Right-click column headers** → add Channel Utilization column

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why Three Views Matter — Choosing the Right Perspective

**AP View — The Overview Perspective:**

```
Best for:
  → Daily operational monitoring
  → Quickly seeing which APs are online/offline
  → Checking overall deployment health
  
What you see:
  AP name | Status | IP | Firmware | Profile | Client count
  Under each AP: Radio 1 | Radio 2 | Radio 3
  
Limitation:
  Radios mixed with different channel loads → hard to spot which specific radio is struggling
```

**Radio View — The Troubleshooting Perspective:**

```
Best for:
  → Identifying overloaded radios
  → Capacity planning
  → Troubleshooting performance issues
  
What you see:
  All radios from ALL APs in one sortable list
  Sort by: utilization → highest utilization radios float to top
  Sort by: client count → most-loaded radios visible immediately
  
Power of this view:
  "Which radio in my entire deployment is most overloaded right now?"
  → Sort by Channel Utilization descending
  → Top entry = the problem radio
  → One click → identify the AP → take action
```

**Group View — The Organized Deployment Perspective:**

```
Best for:
  → Deployments with APs organized by floor, building, or function
  → Quick verification that all APs in a specific area are online
  → Rolling out changes to a subset of APs
  
Example:
  Group "Floor-1": 12 APs → all green → Floor 1 healthy
  Group "Floor-3": 10 APs → 2 red → Floor 3 has issues
  → Immediately know WHERE the problem is without scrolling through 200 APs
```

> 💡 **Real-world analogy:** The three views are like looking at a city's traffic system from different perspectives:
>
> - AP View = satellite overview (see all roads)
> - Radio View = traffic density map (find the most congested roads)
> - Group View = district map (see which neighborhoods have problems)

---

### Channel Utilization Column — Adding It

**Why Channel Utilization is hidden by default:**

- FortiGate only queries APs for utilization data when the column is displayed
- Querying 200 APs continuously for this data without displaying it wastes resources
- Hidden by default → only computed and displayed when admin explicitly adds the column

**How to add it:**

```
WiFi & Switch Controller > Managed FortiAPs
  → Right-click on any column header
  → Column chooser appears
  → Check: "Channel Utilization"
  → Column appears with real-time utilization percentages
```

**Interpreting Channel Utilization:**

```
0-50%:   Healthy — plenty of capacity available
50-70%:  Moderate load — monitor closely
70-85%:  High load — performance may degrade, plan capacity expansion
85-100%: Critical — users experiencing poor performance, immediate action needed
```

---

### Sorting in Radio View — The Most Powerful Monitoring Action

```
Radio View → Click "Channel Utilization" column header to sort descending

Result:
  Top of list:
  FAP-Floor3-NE | Radio 2 | 5 GHz | CH 36 | Utilization: 94% | Clients: 45
  FAP-Conference-A | Radio 1 | 2.4 GHz | CH 6 | Utilization: 87% | Clients: 38
  FAP-Lobby-1 | Radio 2 | 5 GHz | CH 40 | Utilization: 72% | Clients: 28
  ...
  
Bottom of list:
  FAP-Warehouse-12 | Radio 1 | 2.4 GHz | CH 1 | Utilization: 3% | Clients: 1
  
Immediate insight:
  → Floor 3 northeast corner AP Radio 2 is critically overloaded
  → Conference room AP is heavily loaded
  → Warehouse APs have massive unused capacity
  
Action:
  → Deploy additional AP on Floor 3 northeast corner
  → Consider reducing TX power on overloaded APs to shrink cell size
  → Consider enabling AP Handoff to distribute conference room load
```

---

---

# 📄 PAGE 287 — Managed FortiAP Table — View More Details

---

## 🖥️ SLIDE SUMMARY

The slide shows the **"View More Details"** panel for individual APs:

**How to access:** Right-click on AP → **"View More Details"**

**Available information and actions:**

| Section                           | Contents                                                        |
|-----------------------------------|-----------------------------------------------------------------|
| **Color-coded health indicators** | Upper-right corner — immediate visual status (Red/Yellow/Green) |
| **Wireless health**               | Overall AP and radio health assessment                          |
| **Wireless capacity**             | Current capacity utilization metrics                            |
| **Spectrum Analysis**             | Real-time RF spectrum view (if AP capable + configured)         |
| **CLI Access**                    | Direct access to AP CLI for troubleshooting                     |
| **Wireless Logs**                 | Detailed AP operation log, filterable by event                  |

**Tabs available:**

- **Default tab:** Configuration + radio status
- **Clients tab:** All clients associated with this AP (drill-down to individual)
- **Logs tab:** Wireless events filtered for this AP only

---

## 📋 DETAILED INSTRUCTOR NOTES

### Color-Coded Health Indicators — Immediate Visual Triage

The three health indicator colors follow the standard Fortinet severity scheme:

```
🟢 GREEN — Healthy
  Radio operating normally
  Channel utilization within acceptable range
  No detected issues
  Client count within normal parameters
  
🟡 YELLOW — Warning
  Elevated channel utilization (borderline)
  Moderate client count approaching capacity
  Non-critical configuration issue
  Minor signal degradation detected
  → Monitor closely — may degrade to red without intervention
  
🔴 RED — Critical
  High channel utilization (>85%)
  Radio down or not responding
  CAPWAP tunnel issues
  Security threat detected
  → Immediate investigation and action required
```

**Why color-coding is critical for operations:**

```
Scenario: 150 APs deployed, admin checks health during morning briefing
  Without color coding: Must read 150 rows of numbers to find issues
  With color coding: Scan visually → 3 red APs immediately visible
  → 30 seconds to identify which APs need attention vs. 5+ minutes
```

---

### Spectrum Analysis — The RF Environment Visualizer

The slide notes: *"If AP is capable and configured for dedicated operation, spectrum analysis can be enabled."*

**What Spectrum Analysis shows:**

```
Real-time frequency spectrum view:
  X-axis: Frequency (MHz) — shows all channels in the band
  Y-axis: Signal strength (dBm) — shows energy level at each frequency
  
What you can identify:
  Your own APs: Clearly visible 20/40/80 MHz channel blocks
  Neighboring APs: Similar channel blocks at different frequencies
  Non-Wi-Fi interference: Irregular shapes not following 802.11 channel patterns
    → Microwave oven: Appears as wide energy blob around 2.45 GHz
    → Bluetooth devices: Frequency hopping pattern visible
    → Cordless phones: Narrowband signal at specific frequency
    → Video surveillance camera (2.4 GHz): Persistent narrowband signal
```

**Spectrum Analysis hardware requirements:**

- Not all FortiAP models support spectrum analysis
- Typically requires **dedicated scan mode** on a radio OR a specific capable model
- Check release notes for your specific FortiAP model

> 💡 **Real-world application:** An office reports intermittent Wi-Fi drops every few hours. Admin checks Spectrum Analysis on the 2.4 GHz radio → sees a wide energy blob at 2.45 GHz appearing and disappearing → identifies a microwave oven in the break room as the interference source → moves APs to 5 GHz OR relocates microwave → problem solved.

---

### CLI Access — Direct AP Troubleshooting

The **CLI Access** option opens a **direct CLI session** to the FortiAP (Telnet/SSH from FortiGate to AP):

**When CLI access is invaluable:**

```
Scenario: AP shows connected in FortiGate but clients report poor performance
  Standard monitoring: Channel utilization 65% — not critical
  CLI Access → deeper investigation:
  
  # iwconfig          → Check radio interface statistics
  # wpa_cli status    → Check WPA status and associations
  # cat /proc/net/wireless → Check error rates, packet drops
  # ping [FortiGate IP] → Test CAPWAP tunnel latency
  
  Finding: 15% packet retry rate on radio (not visible in GUI)
  → Root cause: Interference on specific sub-channel
  → Action: Change channel, reduce TX power
```

**Common FortiAP CLI diagnostic commands:**

```bash
cw_debug all                        # Enable all debug output
cfg -s                              # Show all AP local configuration
iwconfig                            # Show radio interface stats
wlanconfig ath0 list                # List associated clients
tcpdump -i eth0 -n                  # Packet capture on wired interface
cat /proc/net/wireless              # Radio error statistics
ping -c 5 [FortiGate IP]           # Test connectivity to controller
```

---

### Wireless Logs — Per-AP Event Filtering

The **Wireless Logs** tab shows events filtered specifically for the selected AP:

```
Full wireless log (all APs): Thousands of events → hard to find specific AP's issues
Per-AP log (this AP only): Filtered view → only events from this AP

Events visible in per-AP log:
  CAPWAP tunnel establishment/drops
  Client associations and disassociations
  Authentication successes and failures
  DHCP events for clients of this AP
  Security events (WIDS detections)
  Configuration push events
  Firmware upgrade events
  Radio restarts/resets
  
How to use:
  AP-Floor3-NE is reporting client connectivity complaints
  Open per-AP logs → filter by "error" or "fail"
  See: Multiple "Authentication failed - incorrect PSK" events
  → Root cause: Some clients have wrong Wi-Fi password
  → Action: Verify clients' saved Wi-Fi credentials
```

---

---

# 📄 PAGE 288 — Wi-Fi Clients Diagnostics

---

## 🖥️ SLIDE SUMMARY

The slide details **Wi-Fi Client diagnostics tools**:

**Location:** `WiFi & Switch Controller > WiFi Clients` → right-click client → **"Diagnostics and Tools"**

**What the diagnostics panel shows:**

| Feature                           | Details                                                 |
|-----------------------------------|---------------------------------------------------------|
| **Color-coded health indicators** | Immediate signal/SNR health status                      |
| **Actions available**             | Quarantine or disassociate the workstation              |
| **Bandwidth graph**               | Historical bandwidth usage (\~10 minutes of data)       |
| **SNR graph**                     | Historical Signal-to-Noise Ratio (\~10 minutes of data) |
| **Connection log**                | Detailed step-by-step connection events                 |

**Connection log captures:**

- Station connection errors
- Wireless connection events (association, authentication steps)
- DHCP events
- Filterable and exportable to text file

---

## 📋 DETAILED INSTRUCTOR NOTES

### Client-Level Diagnostics — Why They're Essential

**The gap between AP monitoring and client experience:**

```
AP monitoring shows:
  Channel utilization: 45% (healthy)
  Client count: 22 (normal)
  
But client reports: "I can't connect to Wi-Fi!"

Client-level diagnostics reveal:
  This specific client: RSSI = -78 dBm (poor signal)
  SNR: 12 dB (marginal)
  Authentication failures: 3 in last 5 minutes
  Reason: "4-way handshake timeout"
  
Root cause:
  Client is at edge of coverage → weak signal → authentication keeps failing
  Action: Move client closer to AP OR deploy additional AP in that area
```

---

### Color-Coded Health Indicators — Per-Client

The health indicators at the client level assess:

**Signal Strength (RSSI):**

```
🟢 GREEN: RSSI > -70 dBm    → Excellent/Good signal
🟡 YELLOW: RSSI -70 to -80  → Marginal signal
🔴 RED: RSSI < -80 dBm      → Poor signal → likely experiencing issues
```

**SNR (Signal-to-Noise Ratio):**

```
🟢 GREEN: SNR > 25 dB       → Excellent — high throughput achievable
🟡 YELLOW: SNR 15-25 dB     → Moderate — reduced throughput
🔴 RED: SNR < 15 dB         → Poor — marginal connectivity, high error rate
```

**Why SNR matters more than RSSI alone:**

```
RSSI -65 dBm in a clean environment:
  Noise floor: -95 dBm
  SNR = -65 - (-95) = 30 dB → Excellent

RSSI -65 dBm near interference source:
  Noise floor elevated to -75 dBm (interference)
  SNR = -65 - (-75) = 10 dB → Poor

Same RSSI → completely different SNR → very different user experience
→ SNR is the more meaningful metric for troubleshooting
```

---

### The Connection Log — The Most Powerful Troubleshooting Tool

The connection log shows the **exact sequence of events** in a wireless connection attempt:

**Successful connection sequence in log:**

```
[14:32:01] Station e4:a4:71:80:bc:27 sent probe request on CH 36
[14:32:01] AP FAP-Floor2-SW responded to probe
[14:32:01] Station sent 802.11 Authentication Request
[14:32:01] AP sent Authentication Response: SUCCESS
[14:32:01] Station sent Association Request
[14:32:01] AP sent Association Response: SUCCESS (AID=7)
[14:32:02] EAPOL 4-way handshake started
[14:32:02] Message 1 sent (ANonce)
[14:32:02] Message 2 received (SNonce + MIC) 
[14:32:02] Message 3 sent (PTK install + GTK)
[14:32:02] Message 4 received (Acknowledgement)
[14:32:02] 4-way handshake COMPLETE - Client authenticated
[14:32:02] DHCP Discover received
[14:32:02] DHCP Offer sent: 10.10.2.45
[14:32:02] DHCP Request received
[14:32:02] DHCP ACK sent - Client online
```

**Failed connection sequence (wrong PSK example):**

```
[14:35:10] Station e4:a4:71:80:bc:27 sent probe request on CH 36
[14:35:10] AP FAP-Floor2-SW responded to probe
[14:35:10] Station sent Authentication Request
[14:35:10] Authentication Response: SUCCESS
[14:35:10] Station sent Association Request
[14:35:10] Association Response: SUCCESS
[14:35:11] EAPOL 4-way handshake started
[14:35:11] Message 1 sent (ANonce)
[14:35:11] Message 2 received (SNonce + MIC)
[14:35:11] ❌ MIC VERIFICATION FAILED - Incorrect PSK
[14:35:11] Deauthentication sent to station: Reason 2 (Previous auth no longer valid)
[14:35:14] Station retried - same result
```

**Immediate diagnostic value:**

- ❌ MIC VERIFICATION FAILED → wrong password on client
- ❌ EAPOL-FAIL → 802.1X authentication rejected by RADIUS
- ❌ Association denied (Code 17) → AP overloaded → AP Handoff issue
- ❌ DHCP timeout → DHCP server unreachable → network issue

---

### Historical Graph — 10-Minute Window

**Important limitation noted in slide:** *"The graphing is limited to approximately 10 minutes of data."*

**Why only 10 minutes:**

- Client data is stored in AP memory (RAM) — not written to disk
- Short-term memory only → sufficient for live troubleshooting
- AP doesn't maintain long-term per-client history (FortiAnalyzer handles long-term)

**Using the 10-minute graph effectively:**

```
Scenario: Client reports "Wi-Fi keeps dropping"
  Admin reproduced: Graph shows bandwidth spikes dropping to 0 every 2-3 minutes
  SNR graph: Dips to <10 dB coinciding with bandwidth drops
  
  Interpretation:
    Regular SNR drops → intermittent interference source
    Every 2-3 minutes → microwave oven being used nearby?
    
  Investigation: Check Spectrum Analysis on same radio at same time
  → Confirms: Microwave turning on every few minutes → drops client SNR → disrupts connection
  → Resolution: Move client to 5 GHz SSID → problem resolved
```

---

### Export Log — Offline Analysis

**Why export the log:**

```
Complex issue: Client experiences sporadic drops over 2 hours
10-minute in-GUI graph: Not sufficient (only shows last 10 minutes)

Solution: Export the log to text file → analyze in offline tools
  → Text file contains all events from entire troubleshooting session
  → Can be searched with grep, Excel, or log analysis tools
  → Can be attached to support ticket for Fortinet TAC assistance
```

---

---

# 📄 PAGE 289 — Wi-Fi Dashboard

---

## 🖥️ SLIDE SUMMARY

The slide introduces the **Wi-Fi Dashboard** — the wireless operations center:

**Location:** `Dashboard > WiFi`

**What it provides:**

- Overview of **FortiAPs, SSIDs, and clients**
- Bands, radios, and **Wi-Fi standards** in use
- Customizable widget-based layout

**Available Widgets:**

| Widget                     | What It Monitors                                  |
|----------------------------|---------------------------------------------------|
| 📡 **FortiAP Status**      | AP devices — online/offline + channel utilization |
| 📊 **Channel Utilization** | Per-AP radio channel utilization + client count   |
| 👥 **Clients By FortiAP**  | Client count per AP + Wi-Fi standard used         |
| 📶 **Signal Strength**     | Client signal strength per band                   |
| 🚨 **Rogue APs**           | Detected rogue APs + interfering SSIDs            |
| 📈 **Historical Clients**  | Real-time client count over selected time frame   |
| 🔍 **Interfering SSIDs**   | FortiAPs reporting interfering external APs       |
| ❌ **Login Failures**       | Failed Wi-Fi login attempts on all SSIDs          |

---

## 📋 DETAILED INSTRUCTOR NOTES

### The Wi-Fi Dashboard — Operations Center Philosophy

The Wi-Fi Dashboard is designed around a **single-screen situational awareness** model:

```
Before Wi-Fi Dashboard (checking individual tools):
  Step 1: Check Managed FortiAPs for offline APs
  Step 2: Check WIDS for rogue APs  
  Step 3: Check logs for login failures
  Step 4: Check FortiView for client distribution
  Step 5: Check channel utilization manually per AP
  → 5+ separate navigations → 5-10 minutes to get full picture
  
With Wi-Fi Dashboard (everything on one screen):
  One screen → all critical metrics visible simultaneously
  → 30 seconds to assess wireless network health
  → Immediate identification of any anomalies
```

---

### Each Widget — Deep Operational Value

---

#### 📡 FortiAP Status Widget

```
Shows:
  Total APs: 48
  Online: 45 (green) | Offline: 3 (red)
  List of offline APs with last-seen timestamps
  
Immediate action:
  3 offline APs → click each → see which APs → investigate
  FAP-Warehouse-7: Last seen 3 hours ago → physical inspection needed
  FAP-Floor2-NW: Last seen 5 minutes ago → likely CAPWAP reconnecting
```

**Channel Utilization indicator in this widget:**

- Mini bar charts showing utilization per AP
- Quick visual scan identifies overloaded APs without navigating elsewhere

---

#### 📊 Channel Utilization Widget

```
Visual representation:
  Horizontal bar chart per AP:
  FAP-Conf-Room-A | Radio 1 2.4G: ████████░░ 80% | 32 clients
  FAP-Conf-Room-A | Radio 2 5GHz: ███░░░░░░░ 30% | 12 clients
  FAP-Floor1-NE   | Radio 1 2.4G: ██░░░░░░░░ 20% | 8 clients
  
Immediate insight:
  → Conference room 2.4 GHz radio is overloaded
  → Conference room 5 GHz radio has spare capacity
  → Enable frequency handoff to move clients from 2.4 to 5 GHz
```

---

#### 👥 Clients By FortiAP Widget

```
Shows:
  Per AP: client count + bar showing Wi-Fi standard distribution
  
  FAP-Office-12: 28 clients [WiFi6: ████████ WiFi5: ██ WiFi4: █]
  FAP-Lobby-1:   15 clients [WiFi6: ███ WiFi5: ████ WiFi4: ████████]
  
Business intelligence:
  Lobby AP: Many older WiFi4 clients → mostly visitors with older devices
  Office AP: Mostly WiFi6 clients → employees with new company laptops
  → Staff APs can be configured to prioritize ax features
  → Lobby APs must remain backward compatible with n/g
```

---

#### 📶 Signal Strength Widget

```
Shows distribution of client signal strengths:
  Excellent (>-60dBm): 65%  ████████████
  Good (-60 to -70):   25%  █████
  Fair (-70 to -80):    8%  ██
  Poor (<-80dBm):       2%  ▌
  
Action triggers:
  If "Poor" clients > 5%: Coverage gaps → consider additional APs
  If "Fair" + "Poor" > 20%: Significant coverage issue → site survey needed
  
Trend over time: Widget shows signal distribution changes
  Yesterday: Poor = 2% → Today: Poor = 8%
  → Something changed → new furniture? Office rearrangement? New interference?
```

---

#### 🚨 Rogue APs Widget

```
Shows:
  Count of detected rogue APs (by category):
  Known Rogues: 3 | Unclassified: 7 | Accepted: 45
  
  Recent detections:
  BSSID aa:bb:cc:dd:ee:ff | SSID "EvilTwin" | Detected 14:32 | On-wire: YES
  → CRITICAL: On-wire rogue AP detected → requires immediate action
  
  BSSID ff:ee:dd:cc:bb:aa | SSID "Neighbor-WiFi" | On-wire: NO
  → External neighbor AP → mark as Accepted
```

---

#### ❌ Login Failures Widget — Security Intelligence

```
Shows:
  Failed login attempts per SSID with timestamps:
  
  Corp-WiFi: 47 failures in last hour
  Guest-WiFi: 3 failures in last hour
  
  Detail breakdown:
  Reason: Wrong PSK — 23 failures  
  Reason: RADIUS rejected — 18 failures
  Reason: 4-way handshake timeout — 6 failures
  
  Security interpretation:
  47 failures on Corp-WiFi → possible brute force attack?
  OR → employees returning after password change with old PSK saved
  
  47 failures from same source MAC → brute force → consider MAC ban
  47 failures from many different MACs → employees with old password → force password update notification
```

---

### Historical Clients Widget — Capacity Planning Tool

```
Timeline graph showing client count:
  
Clients (y-axis)
   200 |                    ▄▄▄▄▄▄
   150 |              ▄▄▄▄▄      ▄▄▄▄▄
   100 |        ▄▄▄▄▄                  ▄▄▄▄
    50 |  ▄▄▄▄▄                             ▄▄▄
     0 +-------------------------------------- time (x-axis)
       8AM   10AM   12PM   2PM   4PM   6PM

Insights:
  Peak: 180 clients at 11 AM and 2 PM
  Lunch dip: 120 clients at 12:30 PM (some leave for lunch)
  After 5 PM: Rapid decline as employees leave
  
Capacity planning:
  Peak 180 clients across 30 APs → avg 6 clients per AP → healthy
  If peak grows to 240 → 8 per AP → still fine
  If peak grows to 360 → 12 per AP → approaching capacity → add APs
```

---

---

# 📄 PAGE 290 — Wi-Fi Map

---

## 🖥️ SLIDE SUMMARY

The slide introduces **Wi-Fi Maps** — visual AP placement on floor plans:

**Location:** `WiFi & Switch Controller > WiFi Maps`

**Key capabilities:**

- Create **multiple Wi-Fi maps** (one per floor, building, site)
- Upload **any image file** as the map background (floor plan photo, architectural drawing)
- **Place FortiAP devices** on the map at their physical locations
- Access **AP diagnostics and tools** by clicking on any AP icon on the map

**What the map shows in real-time:**

- AP status (online/offline — color indicator)
- Channel and frequency information
- Alerts and warnings

---

## 📋 DETAILED INSTRUCTOR NOTES

### Wi-Fi Maps — The Visual Management Tool

**The problem Wi-Fi Maps solve:**

```
Without Wi-Fi Maps:
  Admin receives alert: "FAP-Floor2-SW-Corner is offline"
  Admin must mentally map that AP name to a physical location
  "Floor 2, southwest corner" — where exactly is that?
  Admin must walk to floor 2 and search for the AP
  → Slow physical response → extended wireless outage
  
With Wi-Fi Maps:
  Alert: "FAP-Floor2-SW-Corner is offline" → map shows RED dot at SW corner of Floor 2
  Admin immediately sees: "The AP near the break room door on Floor 2"
  Admin can dispatch the right person directly
  → Faster resolution → shorter outage
```

---

### Creating a Useful Wi-Fi Map — Best Practices

**Step 1: Prepare the floor plan image:**

```
Good floor plan image:
  Clear room labels: "Conference Room A", "Break Room", "Server Room"
  Corridors and walls visible
  Scale accurate → helps estimate coverage areas
  File formats: PNG, JPG, SVG, GIF — all supported
  
Source: Architectural drawings, fire evacuation maps, Google Maps screenshots
```

**Step 2: Upload and create the map:**

```
WiFi & Switch Controller > WiFi Maps > Create New
  Name: "Office Building - Floor 2"
  Upload: floor2_plan.png
```

**Step 3: Place FortiAP devices:**

```
From FortiAP list on right panel:
  Drag FAP-Floor2-NE → drop on northeast corner of floor plan
  Drag FAP-Floor2-SW → drop on southwest corner
  Drag FAP-Floor2-Conf → drop on conference room
  → Each AP icon shows current status (green/yellow/red)
```

---

### Real-Time Status on the Map

**What each AP icon shows:**

```
Icon color:
  🟢 Green = Online, healthy
  🟡 Yellow = Online, warning (high utilization, degraded performance)
  🔴 Red = Offline or critical issue

Hovering over AP icon shows:
  AP name: FAP-Floor2-NE
  Status: Online
  Radio 1: 2.4 GHz CH 6 | Utilization 45% | 12 clients
  Radio 2: 5 GHz CH 36 | Utilization 28% | 18 clients
  
Clicking AP icon:
  Opens diagnostics and tools panel
  → Same as right-click "View More Details" from Managed FortiAPs table
  → Access: Logs, CLI, Spectrum Analysis, client list
```

---

### Multiple Maps — Multi-Floor and Multi-Site Management

```
Organization: 5-story office building

Maps created:
  Wi-Fi Map: "Floor 1 - Reception & Lobby"    → 8 APs placed
  Wi-Fi Map: "Floor 2 - Open Office"           → 22 APs placed
  Wi-Fi Map: "Floor 3 - Engineering"           → 18 APs placed
  Wi-Fi Map: "Floor 4 - Executive Suite"       → 10 APs placed
  Wi-Fi Map: "Floor 5 - Data Center"           → 6 APs placed

Navigation:
  Admin switches between floor maps using tabs/dropdown
  All floors' AP statuses updating in real-time
  → Complete visual management of entire building from one screen
  
For multi-site:
  Wi-Fi Map: "HQ - Main Building"
  Wi-Fi Map: "Branch Office - London"
  Wi-Fi Map: "Branch Office - Singapore"
  → Global wireless estate managed visually
```

---

### Wi-Fi Maps vs. FortiPresence Site Management — The Difference

| Feature            | Wi-Fi Maps (FortiGate)                     | FortiPresence Site Management             |
|--------------------|--------------------------------------------|-------------------------------------------|
| **Purpose**        | AP status and operational monitoring       | Visitor analytics and presence data       |
| **What's shown**   | AP icons with status, channel, utilization | Heat maps of visitor density              |
| **Real-time data** | AP health and radio metrics                | Visitor location and density              |
| **Use case**       | Network operations team                    | Business analytics/marketing team         |
| **Complexity**     | Simple — drag and drop APs                 | Complex — areas, assets, RSSI calibration |
| **Primary user**   | Network admin                              | Business analyst, store manager           |

Both can use the same floor plan image — they serve fundamentally different audiences and purposes.

---

---

# 📊 Master Summary — Pages 286–290

```
┌──────────────────────────────────────────────────────────────────────────┐
│           WIRELESS MONITORING TOOLS — COMPLETE GUIDE                     │
├─────────────────────────┬────────────────────────────────────────────────┤
│ TOOL                    │ KEY FACTS & BEST USES                         │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Managed FortiAPs Table  │ Three views: AP / Radio / Group               │
│                         │ Radio View: sort by utilization → find problems│
│                         │ Add Channel Utilization via right-click header │
│                         │ Right-click AP → View More Details            │
│                         │ → Health indicators, Spectrum Analysis, CLI    │
│                         │ Tabs: Config | Clients | Logs (per-AP filtered)│
├─────────────────────────┼────────────────────────────────────────────────┤
│ Health Indicators       │ 🟢 Green = Healthy                            │
│ (AP + Client level)     │ 🟡 Yellow = Warning (monitor closely)         │
│                         │ 🔴 Red = Critical (immediate action needed)   │
│                         │ Assess: RSSI, SNR, utilization, client count  │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Spectrum Analysis       │ Requires: dedicated monitor mode + capable AP │
│                         │ Shows: real-time RF spectrum (all channels)    │
│                         │ Identifies: Wi-Fi APs + non-Wi-Fi interference │
│                         │ Use for: finding microwave, cordless phone RFI │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Wi-Fi Client Diagnostics│ Location: WiFi Clients → right-click →        │
│                         │           Diagnostics and Tools               │
│                         │ Actions: Quarantine or Disassociate client    │
│                         │ Bandwidth graph: ~10 min historical data       │
│                         │ SNR graph: ~10 min historical data            │
│                         │ Connection log: step-by-step 802.11 events    │
│                         │ → Best tool for diagnosing wrong PSK/RADIUS   │
│                         │ Exportable to text file for offline analysis  │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Wi-Fi Dashboard         │ Location: Dashboard > WiFi                    │
│                         │ 8 widgets: FortiAP Status, Channel Util,      │
│                         │   Clients By FortiAP, Signal Strength,        │
│                         │   Rogue APs, Historical Clients,              │
│                         │   Interfering SSIDs, Login Failures           │
│                         │ Single-screen network health overview         │
│                         │ Login Failures: security/troubleshooting intel │
│                         │ Historical Clients: capacity planning tool     │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Wi-Fi Maps              │ Location: WiFi & Switch Controller > WiFi Maps│
│                         │ Upload any image → place AP icons             │
│                         │ Real-time AP status on floor plan             │
│                         │ Click AP icon → access diagnostics + CLI      │
│                         │ Multiple maps: per floor, building, site       │
│                         │ Speeds physical troubleshooting response       │
└─────────────────────────┴────────────────────────────────────────────────┘

⚠️  KEY OPERATIONAL TIPS:
  1. Radio View + sort by Channel Utilization = fastest way to find overloaded radios
  2. Client Connection Log = first tool to check for any client connectivity complaint
  3. SNR is more meaningful than RSSI alone — check BOTH
  4. Login Failures widget: same MAC = brute force; many MACs = password change issue
  5. Spectrum Analysis requires dedicated scan mode — not available on all models
  6. Client history graph = 10 minutes only — export log for longer session analysis
  7. Wi-Fi Map speeds physical response — always maintain accurate AP placement
  8. Historical Clients widget = capacity planning — trend over weeks/months
```

---

### Topic: Advanced Options and Monitoring — FortiAP REST API

---

---

# 📄 PAGE 294 — REST API Overview

---

## 🖥️ SLIDE SUMMARY

The slide introduces **FortiAP REST API** — programmatic access to FortiAP management:

**What it is:**

- REST (Representational State Transfer) API integration supported on **FortiAP**
- Uses standard **HTTP methods** to handle requests (GET, POST, PUT, DELETE)

**Two API specifications:**

| API Type        | Purpose                                                                                |
|-----------------|----------------------------------------------------------------------------------------|
| **CMDB API**    | Retrieve and **modify CLI configurations** — create, edit, delete objects              |
| **Monitor API** | Perform **specific actions** on endpoint resources — restart, shutdown, backup/restore |

**Two authentication methods:**

| Method            | How It Works                                           | Scope                  |
|-------------------|--------------------------------------------------------|------------------------|
| **Session-based** | Authenticate via login → valid for duration of session | Per login session      |
| **Token-based**   | Single API token used for all calls                    | Per token (persistent) |

---

## 📋 DETAILED INSTRUCTOR NOTES

### What Is REST and Why It Matters

**REST (Representational State Transfer)** is an architectural style for building web APIs:

```
Core REST principles:
  1. Client-Server: Client and server are separate — communicate via HTTP
  2. Stateless: Each request contains all information needed — no server-side session
  3. Uniform Interface: Consistent, predictable URL patterns and HTTP methods
  4. Resource-Based: Everything is a "resource" accessed via a URL

Why REST is popular for network automation:
  → Simple: Standard HTTP — any programming language can use it
  → Lightweight: JSON/XML payloads — no complex protocols
  → Predictable: Consistent URL structure → easy to learn
  → Cloud-native: Perfect for automation pipelines, CI/CD, cloud orchestration
```

---

### Why FortiAP Needs a REST API

**Traditional FortiAP management (manual):**

```
Admin wants to check status of 200 APs:
  → Log into FortiGate GUI
  → Navigate to Managed FortiAPs
  → Check each AP manually
  → Repeat daily
  → Time: 30-60 minutes per check
  
Admin wants to restart 50 APs after firmware update:
  → Right-click each AP → restart
  → 50 individual operations
  → Time: 20+ minutes
```

**REST API-driven management (automated):**

```python
# Python script: Check all AP statuses automatically
import requests

aps = ["10.0.13.3", "10.0.13.4", "10.0.13.5", ...200 IPs...]

for ap_ip in aps:
    response = requests.get(f"https://{ap_ip}/api/v1/sys-status",
                           headers={"Authorization": f"Bearer {token}"},
                           verify=False)
    status = response.json()
    if status['result'] != 'ok':
        alert_team(f"AP {ap_ip} has issues: {status}")
        
# Runs in seconds, handles 200 APs automatically
# Can run every 5 minutes as a scheduled job
```

> 💡 **Real-world analogy:** The REST API is like giving your network monitoring system **hands** — instead of a human having to click through the GUI to check each AP, automation scripts can query and control APs as fast as HTTP requests can travel. This is the foundation of **network-as-code** operations.

---

### CMDB API vs. Monitor API — Deep Distinction

**CMDB (Configuration Management Database) API:**

```
Purpose: Read and write CONFIGURATION objects

GET operations (read):
  → Get current radio configuration
  → Get current SSID settings
  → Get AP's current variable values
  → Get system performance metrics

POST/PUT operations (write):
  → Change a configuration variable
  → Update radio settings
  → Modify SSID parameters

Analogy: Like editing a configuration file — reads and writes settings
```

**Monitor API:**

```
Purpose: Trigger ACTIONS on the AP — not configuration changes

Actions:
  → Reboot the AP
  → Shut down the AP
  → Backup the configuration file
  → Restore from a backup
  → Execute operational commands

Analogy: Like pressing buttons — triggers something to happen, not changing settings
```

**Why the distinction matters:**

```
CMDB API call: "Set radio 1 channel to 36"
  → Changes the stored configuration
  → Setting persists after reboot
  → Can be read back with GET to confirm change

Monitor API call: "Reboot this AP"
  → Triggers an immediate action
  → Cannot be "read back" — it's a one-way command
  → AP restarts immediately after receiving the request
```

---

### Session-Based vs. Token-Based Authentication

**Session-Based Authentication:**

```
Step 1: POST credentials to AP login endpoint
  POST https://10.0.13.3/api/v1/login
  Body: {"username": "admin", "password": "password123"}
  
Step 2: AP returns session cookie
  Set-Cookie: APPSESSION=abc123xyz; Path=/; HttpOnly
  
Step 3: Include cookie in subsequent requests
  GET https://10.0.13.3/api/v1/sys-status
  Cookie: APPSESSION=abc123xyz
  
Step 4: Session expires on logout or timeout
  POST https://10.0.13.3/api/v1/logout
  
Use case: Interactive scripts, short-lived automation tasks
Limitation: Session expires → must re-authenticate
```

**Token-Based Authentication:**

```
Step 1: Generate API token in FortiAP (one-time setup)
  FortiAP GUI → Admin → API Tokens → Generate Token
  Token: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
  
Step 2: Include token in every API request header
  GET https://10.0.13.3/api/v1/sys-status
  Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
  
Step 3: Token remains valid until revoked
  No re-authentication needed
  
Use case: Persistent automation, monitoring scripts, CI/CD pipelines
Advantage: No session management needed → simpler automation code
```

**Security comparison:**

```
Session-based:
  ✅ Credentials validated per session → new compromise requires new credential theft
  ❌ More complex code (must handle session management, timeouts, re-auth)
  
Token-based:
  ✅ Simpler code → less chance of authentication bugs
  ❌ If token is compromised → attacker has persistent access until token revoked
  → Store tokens securely (environment variables, secrets managers)
  → Never hardcode tokens in source code
  → Rotate tokens regularly
```

> ⚠️ **Trainer Warning:** API tokens are **as sensitive as passwords** — arguably more so because they don't expire by default. Always:
>
> - Store tokens in a **secrets manager** (HashiCorp Vault, AWS Secrets Manager, etc.)
> - **Never commit tokens to source code repositories** (Git)
> - **Rotate tokens** periodically and after any suspected compromise
> - **Restrict token permissions** to the minimum required operations

---

---

# 📄 PAGE 295 — API REST Calls

---

## 🖥️ SLIDE SUMMARY

The slide provides a **complete reference of REST API calls** using GET and POST methods:

**REST Calls Using GET HTTP Method (Read Operations):**

| Operation                             | API Endpoint URL                      |
|---------------------------------------|---------------------------------------|
| **List effective variables**          | `https://[AP-IP]/api/v1/cfg-get`      |
| **List all variables**                | `https://[AP-IP]/api/v1/cfg-meta-get` |
| **Get current radios configuration**  | `https://[AP-IP]/api/v1/radio-cfg`    |
| **Get system performance values**     | `https://[AP-IP]/api/v1/sys-perf`     |
| **Get system status**                 | `https://[AP-IP]/api/v1/sys-status`   |
| **Get current SSIDs**                 | `https://[AP-IP]/api/v1/vap-cfg`      |
| **Get current FortiAP configuration** | `https://[AP-IP]/api/v1/wtp-cfg`      |

**REST Calls Using POST HTTP Method (Write/Action Operations):**

| Operation                   | API Endpoint URL                 |
|-----------------------------|----------------------------------|
| **Add or change variables** | `https://[AP-IP]/api/v1/cfg-set` |
| **Reboot FortiAP**          | `https://[AP-IP]/api/v1/reboot`  |

---

## �� DETAILED INSTRUCTOR NOTES

### HTTP Methods — The Foundation of REST

Before analyzing each endpoint, understanding HTTP methods is essential:

```
GET  → READ — Retrieve data, no changes made
      "What is the current radio configuration?"
      Safe: Can call GET repeatedly with no side effects

POST → WRITE/CREATE/ACTION — Send data to create or trigger something
      "Change this setting" or "Do this action"
      Not safe: Changes state or triggers actions

PUT  → UPDATE — Replace a resource entirely
      "Replace this entire configuration object"

DELETE → REMOVE — Delete a resource
      "Remove this SSID from the configuration"
```

**REST convention:**

- **GET** = reading information → data returned in response body
- **POST** = sending data or commands → action executed, result returned

---

### Each GET Endpoint — Detailed Analysis

---

#### `GET /api/v1/cfg-get` — List Effective Variables

```
Purpose: Retrieve CURRENT EFFECTIVE configuration variables
         "What settings is this AP actually running with right now?"

Why "effective" is important:
  AP may have default values for unconfigured settings
  AP may have overrides from different sources (controller vs. local)
  "Effective" = the actual value in use → what matters operationally

Example use:
  You pushed a new configuration → verify it was applied correctly
  → GET cfg-get → compare with expected values

Example response (JSON):
{
  "AC_IPADDR_1": "192.168.1.1",
  "AC_DISCOVERY_TYPE": "1",
  "ADMIN_PASSWD": "***",
  "MESH_AP_TYPE": "0",
  ...
}
```

---

#### `GET /api/v1/cfg-meta-get` — List ALL Variables

```
Purpose: Retrieve COMPLETE list of ALL available configuration variables
         Including: defaults, descriptions, valid value ranges

When to use:
  Exploring what configurations are possible
  Building automation that needs to know all configurable parameters
  Documentation generation for configuration management

Difference from cfg-get:
  cfg-get → shows current values
  cfg-meta-get → shows ALL possible parameters + their metadata
  (like a man page for the configuration)
```

---

#### `GET /api/v1/radio-cfg` — Get Current Radio Configuration

```
Purpose: Retrieve radio-specific settings for all radios on this AP

Data returned (example):
{
  "radio1": {
    "band": "802.11ax-2G",
    "channel": "6",
    "channel-width": "20MHz",
    "txpower": "17",
    "mode": "access-point",
    "ssids": ["Corp-WiFi", "Guest-WiFi"]
  },
  "radio2": {
    "band": "802.11ax-5G",
    "channel": "36",
    "channel-width": "80MHz",
    "txpower": "15",
    "mode": "access-point",
    "ssids": ["Corp-WiFi"]
  }
}

Automation use case:
  Daily script verifies all APs are on correct channels
  If channel drift detected → alert and auto-correct via cfg-set
```

---

#### `GET /api/v1/sys-perf` — Get System Performance Values

```
Purpose: Real-time performance metrics of the AP hardware itself

Data returned (example):
{
  "cpu-usage": 23,        → CPU utilization percentage
  "mem-usage": 45,        → RAM utilization percentage
  "uptime": 1209600,      → Seconds since last boot (14 days)
  "tx-bytes": 15234567890, → Total bytes transmitted
  "rx-bytes": 8765432100,  → Total bytes received
  "temperature": 42        → CPU/board temperature in Celsius
}

Critical monitoring use cases:
  CPU > 80% sustained: AP under heavy load → may drop management frames
  Memory > 90%: Risk of AP instability → consider restart
  Temperature > 70°C: Thermal issue → check ventilation
  
Automated alerting:
  Monitoring script polls sys-perf every minute
  If cpu-usage > 80% for 5+ consecutive minutes → PagerDuty alert
```

---

#### `GET /api/v1/sys-status` — Get System Status

```
Purpose: Overall AP system health and operational status

Data returned (example):
{
  "result": "ok",
  "model": "FAP-231F",
  "serial": "FP231FTF23032211",
  "firmware": "7.4.3-build1234",
  "controller-ip": "192.168.1.1",
  "controller-status": "connected",
  "capwap-state": "run",
  "uptime": 1209600
}

Key fields to check:
  result: "ok" → AP is healthy
  controller-status: "connected" → CAPWAP tunnel up
  capwap-state: "run" → fully operational
  
If result ≠ "ok" → immediate investigation needed
If controller-status ≠ "connected" → CAPWAP tunnel down → critical

Automation use case:
  Health check script runs every 5 minutes across all APs
  Any AP with controller-status ≠ "connected" → alert network team
  → Proactive detection before users report issues
```

---

#### `GET /api/v1/vap-cfg` — Get Current SSIDs

```
Purpose: Retrieve SSID (VAP = Virtual Access Point) configuration

Data returned (example):
{
  "ssids": [
    {
      "ssid": "Corp-WiFi",
      "security": "wpa3-enterprise",
      "radio": ["radio1", "radio2"],
      "vlan": "10",
      "tunnel": true
    },
    {
      "ssid": "Guest-WiFi", 
      "security": "open",
      "radio": ["radio1"],
      "vlan": "50",
      "captive-portal": true
    }
  ]
}

Automation use case:
  Compliance check: verify all APs are broadcasting required SSIDs
  If "Corp-WiFi" missing from any AP → alert and re-push configuration
  Verify no unauthorized SSIDs were added → rogue SSID detection
```

---

#### `GET /api/v1/wtp-cfg` — Get Current FortiAP Configuration

```
Purpose: Retrieve the complete FortiAP (WTP = Wireless Termination Point) configuration
         Most comprehensive configuration retrieval endpoint

Data returned:
  All AP-level settings including:
  Controller discovery settings
  AP profile assignments
  All radio configurations
  All SSID assignments
  Local FortiAP settings

Use case:
  Configuration backup → save current config to file before changes
  Configuration comparison → before/after change verification
  Audit → document current state for compliance records
```

---

### POST Endpoints — Write and Action Operations

---

#### `POST /api/v1/cfg-set` — Add or Change Variables

```
Purpose: Modify FortiAP configuration variables

Request body (JSON):
{
  "AC_IPADDR_1": "10.0.0.1",       → Set primary controller IP
  "AC_DISCOVERY_TYPE": "1"          → Set discovery type to static
}

Example use cases:
  Change controller IP when migrating to new FortiGate
  Update mesh configuration remotely
  Set local admin password programmatically
  Configure discovery settings at scale
  
Response on success:
{
  "result": "ok",
  "changed": ["AC_IPADDR_1", "AC_DISCOVERY_TYPE"]
}

Critical note: 
  Some changes require AP reboot to take effect
  Follow cfg-set with a reboot call if needed
```

---

#### `POST /api/v1/reboot` — Reboot FortiAP

```
Purpose: Remotely restart the FortiAP

Request body:
{} (empty — no parameters needed)

OR with delay:
{
  "delay": 30    → Wait 30 seconds before rebooting
                  → Allows time to send acknowledgment, clean up sessions
}

Use cases:
  Apply configuration changes that require restart
  Recover an AP that is degraded but still responsive
  Scheduled maintenance reboots at 3 AM
  After firmware push → trigger restart to activate new firmware

Response:
{
  "result": "ok",
  "message": "Rebooting in 30 seconds"
}

⚠️ WARNING: After reboot, AP drops all client connections!
  → Always schedule during maintenance window
  → Notify users in advance
  → Consider Hitless Rolling Upgrade for multi-AP restarts
```

---

### Practical Automation Examples — Real-World Scripts

**Example 1 — Daily Health Check Script:**

```python
import requests
import json
from datetime import datetime

TOKEN = "your-api-token-here"  # Should come from secrets manager
AP_LIST = ["10.0.13.3", "10.0.13.4", "10.0.13.5"]

def check_ap_health(ap_ip):
    headers = {"Authorization": f"Bearer {TOKEN}"}
    url = f"https://{ap_ip}/api/v1/sys-status"
    
    try:
        response = requests.get(url, headers=headers, verify=False, timeout=10)
        status = response.json()
        
        if status.get('result') != 'ok':
            return f"❌ {ap_ip}: UNHEALTHY - {status}"
        if status.get('controller-status') != 'connected':
            return f"⚠️  {ap_ip}: CAPWAP DOWN - Controller disconnected"
        return f"✅ {ap_ip}: Healthy | Firmware: {status.get('firmware')}"
    except Exception as e:
        return f"🔴 {ap_ip}: UNREACHABLE - {e}"

print(f"=== AP Health Check {datetime.now()} ===")
for ap in AP_LIST:
    print(check_ap_health(ap))
```

**Output:**

```
=== AP Health Check 2024-05-03 08:00:01 ===
✅ 10.0.13.3: Healthy | Firmware: 7.4.3-build1234
⚠️  10.0.13.4: CAPWAP DOWN - Controller disconnected
✅ 10.0.13.5: Healthy | Firmware: 7.4.3-build1234
```

---

**Example 2 — Batch Reboot Script (with delay):**

```python
import requests
import time

TOKEN = "your-api-token-here"
AP_LIST = ["10.0.13.3", "10.0.13.4", "10.0.13.5"]

headers = {"Authorization": f"Bearer {TOKEN}"}

print("Starting batch reboot at 3:00 AM maintenance window...")
for i, ap_ip in enumerate(AP_LIST):
    url = f"https://{ap_ip}/api/v1/reboot"
    response = requests.post(url, 
                            headers=headers,
                            json={"delay": 10},
                            verify=False)
    
    if response.json().get('result') == 'ok':
        print(f"✅ {ap_ip}: Reboot initiated")
    else:
        print(f"❌ {ap_ip}: Reboot failed - {response.json()}")
    
    time.sleep(60)  # Wait 60 seconds between each AP reboot
                    # Prevents all APs from rebooting simultaneously
                    # Maintains some wireless coverage during rolling restart
```

---

**Example 3 — Compliance Check (Verify SSIDs):**

```python
import requests

TOKEN = "your-api-token-here"
AP_LIST = ["10.0.13.3", "10.0.13.4"]
REQUIRED_SSIDS = ["Corp-WiFi", "Guest-WiFi"]

headers = {"Authorization": f"Bearer {TOKEN}"}

for ap_ip in AP_LIST:
    url = f"https://{ap_ip}/api/v1/vap-cfg"
    response = requests.get(url, headers=headers, verify=False)
    data = response.json()
    
    current_ssids = [ssid['ssid'] for ssid in data.get('ssids', [])]
    
    for required in REQUIRED_SSIDS:
        if required not in current_ssids:
            print(f"⚠️ COMPLIANCE FAIL: {ap_ip} missing SSID '{required}'")
        else:
            print(f"✅ {ap_ip}: '{required}' present")
    
    # Check for unauthorized SSIDs
    unauthorized = [s for s in current_ssids if s not in REQUIRED_SSIDS]
    if unauthorized:
        print(f"🚨 SECURITY ALERT: {ap_ip} has UNAUTHORIZED SSIDs: {unauthorized}")
```

---

---

# 📊 Master Summary — Pages 294–295

```
┌──────────────────────────────────────────────────────────────────────────┐
│              FORTIAP REST API — COMPLETE GUIDE                           │
├─────────────────────────┬────────────────────────────────────────────────┤
│ TOPIC                   │ KEY FACTS                                      │
├─────────────────────────┼────────────────────────────────────────────────┤
│ What REST API Is        │ HTTP-based programmatic access to FortiAP      │
│                         │ Enables automation, monitoring, bulk ops       │
│                         │ Standard HTTP GET/POST methods                 │
│                         │ JSON request/response format                   │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Two API Specs           │ CMDB API: Read/write CLI configurations        │
│                         │   → Create, edit, delete config objects        │
│                         │ Monitor API: Trigger actions on AP             │
│                         │   → Restart, shutdown, backup, restore         │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Authentication Methods  │ Session-based: Login → session cookie → logout │
│                         │   → Per-session validity                       │
│                         │   → More re-auth management needed             │
│                         │ Token-based: API token in Authorization header │
│                         │   → Persistent until revoked                   │
│                         │   → Simpler for automation                     │
│                         │   ⚠️ Treat token like password — store securely│
├─────────────────────────┼────────────────────────────────────────────────┤
│ GET Endpoints (Read)    │ /api/v1/cfg-get → current effective variables  │
│                         │ /api/v1/cfg-meta-get → ALL possible variables  │
│                         │ /api/v1/radio-cfg → radio configuration        │
│                         │ /api/v1/sys-perf → CPU/RAM/temp/traffic stats  │
│                         │ /api/v1/sys-status → AP health + CAPWAP state  │
│                         │ /api/v1/vap-cfg → SSID configurations          │
│                         │ /api/v1/wtp-cfg → full AP configuration        │
├─────────────────────────┼────────────────────────────────────────────────┤
│ POST Endpoints (Write)  │ /api/v1/cfg-set → modify configuration vars   │
│                         │ /api/v1/reboot → remotely restart the AP      │
│                         │   ⚠️ Reboot drops all client connections!      │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Key Automation Uses     │ Bulk health monitoring (sys-status polling)    │
│                         │ Configuration compliance verification          │
│                         │ Rolling restart scripts (post-firmware)        │
│                         │ Unauthorized SSID detection                    │
│                         │ Channel configuration drift detection          │
│                         │ Performance threshold alerting                 │
└─────────────────────────┴────────────────────────────────────────────────┘

⚠️  CRITICAL RULES TO REMEMBER:
  1. GET = safe, read-only; POST = changes state or triggers actions
  2. Token-based auth is simpler for automation — but protect tokens like passwords
  3. NEVER hardcode API tokens in source code — use secrets managers
  4. cfg-get = current values; cfg-meta-get = ALL possible parameters
  5. sys-status is the primary health check endpoint — poll this for monitoring
  6. sys-perf gives hardware metrics — CPU/RAM/temperature for capacity planning
  7. /api/v1/reboot IMMEDIATELY disconnects all clients — always use in maintenance window
  8. Some cfg-set changes require a reboot to take effect — check docs per variable
  9. Token-based auth header format: "Authorization: Bearer [token]"
```

---

### Topic: Advanced Options and Monitoring — Syslog Profiles & Wi-Fi Event Logs

---

---

# 📄 PAGE 299 — Syslog Profile

---

## 🖥️ SLIDE SUMMARY

The slide introduces **Syslog Profiles** for FortiAP — sending AP logs to an external syslog server:

**What it does:**

- FortiAP sends **event logs** and **UTM logs** directly to an external syslog server
- Available for FortiAPs managed by **FortiGate OR FortiLAN Cloud**

**Key configuration details:**

| Setting              | Detail                                                                          |
|----------------------|---------------------------------------------------------------------------------|
| **Where configured** | Inside FortiAP profile settings (`WiFi & Switch Controller > FortiAP Profiles`) |
| **Assignment**       | Can be assigned to **multiple** FortiAP profiles simultaneously                 |
| **Log level**        | Specified within the syslog profile                                             |
| **Port number**      | CLI only — default **514**                                                      |

**CLI Configuration:**

```bash
config wireless-controller syslog-profile
    edit "SYSLOG_PROFILE"
        set server-port 514    # Standard syslog UDP port
    next
end
```

**GUI Path:** `WiFi & Switch Controller > FortiAP Profiles`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Why FortiAP Needs Its Own Syslog Profile

**Understanding the logging architecture:**

```
Standard FortiGate logging (without syslog profile):
  FortiAP → CAPWAP tunnel → FortiGate → FortiGate processes log → stores locally
                                      → or forwards to FortiAnalyzer
  
  FortiGate acts as LOG AGGREGATOR for all AP events
  AP itself doesn't send logs anywhere — FortiGate handles it centrally
  
With FortiAP Syslog Profile:
  FortiAP → CAPWAP tunnel → FortiGate (still functions normally)
  FortiAP → ALSO → direct syslog messages → External Syslog Server
  
  AP sends its own logs DIRECTLY to syslog server
  Bypasses the FortiGate logging pipeline
  Two parallel log streams from the same AP
```

**Why this dual-path logging matters:**

```
Scenario 1 — Compliance requirement:
  Security policy: ALL network device logs must go to central SIEM (Splunk, IBM QRadar)
  FortiGate logs: Already forwarding to SIEM ✅
  FortiAP-level logs: Without syslog profile → SIEM misses AP-level events ❌
  With syslog profile → FortiAP logs also reach SIEM ✅
  → Full compliance achieved

Scenario 2 — FortiGate failure:
  FortiGate goes offline (hardware failure, reboot)
  Without syslog profile: ALL AP logging stops → blind spot during outage
  With syslog profile: FortiAP continues logging directly to syslog server
  → Continuous audit trail even during controller failure → forensic continuity

Scenario 3 — FortiLAN Cloud deployment (no local FortiGate):
  APs managed from cloud → no local FortiGate to aggregate logs
  With syslog profile: APs send logs directly to on-premises syslog server
  → Local log retention even in cloud-managed deployments
```

---

### What Types of Logs Are Sent

**Event Logs — AP operational events:**

```
Examples:
  CAPWAP tunnel established/dropped
  Client association/disassociation events
  Authentication successes and failures
  Radio restarts and channel changes
  Firmware upgrade events
  Configuration push events
  DHCP events for wireless clients
  Rogue AP detections (from WIDS)
  Wireless attack detections (deauth flood, EAPOL flood, etc.)
```

**UTM Logs — Security inspection events (if applicable):**

```
Available on FortiAP-U models with UTP (Unified Threat Protection):
  Web filter blocks (malicious URLs accessed from wireless clients)
  Antivirus detections
  IPS signature matches
  Application control events
  Botnet communication attempts
  
These are the same security events FortiGate generates — but from the AP's
local UTM inspection (Bridge Mode with security profiles)
```

---

### Syslog Log Levels — Controlling Verbosity

The slide states: *"Specify the log level in the syslog profile."*

**Standard syslog severity levels (RFC 5424):**

| Level             | Numeric | Description               | When to Use                     |
|-------------------|---------|---------------------------|---------------------------------|
| **Emergency**     | 0       | System unusable           | Critical AP failures            |
| **Alert**         | 1       | Immediate action required | CAPWAP tunnel permanent failure |
| **Critical**      | 2       | Critical conditions       | Radio hardware failure          |
| **Error**         | 3       | Error conditions          | Authentication errors           |
| **Warning**       | 4       | Warning conditions        | High channel utilization alerts |
| **Notice**        | 5       | Normal but significant    | Client associations             |
| **Informational** | 6       | Informational messages    | Normal operations               |
| **Debug**         | 7       | Debug-level messages      | Detailed troubleshooting        |

**Choosing the right log level:**

```
Production environment: Level 4 (Warning) or Level 5 (Notice)
  → Captures important events without excessive noise
  → Syslog server won't be overwhelmed with trivial messages
  
Troubleshooting: Level 6 (Informational) or Level 7 (Debug)
  → Maximum detail → captures every event
  → HIGH volume → only enable temporarily → switch back after diagnosis
  
Security compliance: Level 4 or 5
  → All warnings and above → sufficient for audit requirements
  → Manageable volume for long-term storage
```

> ⚠️ **Trainer Warning:** Setting log level to **Debug (7)** in production generates **massive log volumes** — potentially thousands of entries per minute per AP. This can:
>
> - Overwhelm the syslog server with traffic
> - Fill syslog server storage rapidly
> - Impact AP performance (CPU cycles spent generating/sending logs)
> - Always revert to Warning/Notice after troubleshooting

---

### Port 514 — Standard Syslog Port

**Why port 514:**

- **UDP 514** is the traditional, standard syslog port defined in RFC 3164
- Virtually every syslog server (Splunk, rsyslog, syslog-ng, IBM QRadar, Graylog) listens on 514 by default
- No firewall exceptions typically needed — it's a universally expected port

**When to change the port:**

```
Scenario: Security policy requires non-standard ports to prevent port scanning
  → Change to custom port (e.g., 5514)
  CLI:
    config wireless-controller syslog-profile
        edit "SYSLOG_PROFILE"
            set server-port 5514    # Custom port
        next
    end
  → Must also configure syslog server to listen on 5514
  → Firewall rules must allow UDP 5514 from AP subnet to syslog server

Scenario: TLS syslog (encrypted) required
  → Traditional syslog on 514 is UNENCRYPTED (plaintext UDP)
  → Encrypted syslog typically uses TCP 6514 (RFC 5425)
  → Check if FortiAP supports TLS syslog in your firmware version
```

> ⚠️ **Security note:** Standard syslog (UDP 514) sends log messages in **plaintext over the network**. In sensitive environments, ensure the syslog traffic path is on a dedicated management VLAN or encrypted (TLS syslog). Syslog messages contain detailed operational information about your wireless network — confidential in security-conscious environments.

---

### Multiple Profile Assignment — Management Flexibility

The slide notes: *"Possibly assigned in multiple FortiAP profiles."*

```
One Syslog Profile → many FortiAP profiles:
  Syslog Profile "SYSLOG_PROFILE":
    Server: 192.168.10.50
    Port: 514
    Level: Warning
    
  FortiAP Profile "Floor-1-APs":      assigned → SYSLOG_PROFILE
  FortiAP Profile "Floor-2-APs":      assigned → SYSLOG_PROFILE
  FortiAP Profile "Conference-APs":   assigned → SYSLOG_PROFILE
  FortiAP Profile "Guest-APs":        assigned → SYSLOG_PROFILE
  
  Change syslog server IP once → ALL AP profiles updated automatically
  → Single point of management for logging destination
```

**Scenario where different profiles use different syslog profiles:**

```
High-security areas (executive floor, server room):
  FortiAP Profile "Secure-APs": assigned → SYSLOG_HIGH_VERBOSITY
    → Log level: Informational (more detailed)
    → Different syslog server (security team's dedicated server)

General office areas:
  FortiAP Profile "Office-APs": assigned → SYSLOG_STANDARD
    → Log level: Warning (standard)
    → Main syslog server
```

---

---

# 📄 PAGE 300 — Wi-Fi Event Logs

---

## 🖥️ SLIDE SUMMARY

The slide covers **Wi-Fi Event Logs** — the historical wireless event view in FortiGate:

**Location:** `Log & Report > Events > WiFi Events`

**What it provides:**

- **Whole-controller historical view** of wireless network events
- Covers events affecting both **clients AND APs** over time

**For viewing specific station (client) information, add columns:**

- **Band** — which frequency band the client was using
- **Data Rate** — connection speed at time of event
- **Physical AP** — which AP the client was connected to

**Then add a filter for:** Station Mac (specific client MAC address)

**For monitoring a specific AP, add column:**

- **Physical AP** — name of the AP

**Then add a filter for:** Physical AP (specific AP name)

**GUI Path:** `Log & Report > Events > WiFi Events`

---

## 📋 DETAILED INSTRUCTOR NOTES

### Wi-Fi Event Logs vs. Per-AP Logs — The Scope Difference

These two log views serve different purposes and operate at different scopes:

```
Per-AP Logs (from Managed FortiAPs → View More Details → Logs tab):
  Scope: ONE specific AP only
  Data: Only events from that physical AP
  Best for: Deep-diving into one AP's issues
  Limitation: Cannot see events across multiple APs simultaneously
  Retention: Limited (in-memory or recent logs only)

Wi-Fi Event Logs (Log & Report > Events > WiFi Events):
  Scope: ENTIRE wireless controller (ALL APs and ALL clients)
  Data: All wireless events across entire deployment
  Best for: Network-wide investigations, historical analysis
  Advantage: Can filter, correlate, and compare across entire infrastructure
  Retention: Depends on FortiGate logging storage policy
```

---

### The Wi-Fi Event Log Table — Default Columns

By default, the WiFi Events table shows basic columns:

```
Default columns:
  Date/Time | Level | Event Type | Message
  
Example entries (default view):
  14:32:01 | Info | Station-Connected | Station connected to Corp-WiFi
  14:32:05 | Info | DHCP-Assigned | IP 10.10.1.45 assigned to client
  14:35:22 | Warning | Auth-Failed | Authentication failed (wrong PSK)
  14:36:01 | Info | Station-Disconnected | Station disconnected from Corp-WiFi
  14:40:15 | Warning | Rogue-AP-Detected | Unknown BSSID aa:bb:cc:dd:ee:ff detected
```

**The limitation:** Without additional columns, you can't easily identify:

- WHICH specific AP was involved
- What band the client was using
- What data rate was achieved
- Which specific client's MAC address

---

### Adding Columns — Transforming the Log into a Diagnostic Tool

**How to add columns:**

```
Log & Report > Events > WiFi Events
  → Click gear icon or column chooser
  → Check: Band, Data Rate, Physical AP, Station Mac
  → Columns appear in the table

Enhanced view:
  Date/Time | Level | Event | Station Mac | Physical AP | Band | Data Rate | Message
  
  14:32:01 | Info | Connected | e4:a4:71:80:bc:27 | FAP-Floor2-NE | 5GHz | 867Mbps | Corp-WiFi
  14:35:22 | Warn | Auth-Fail | cc:dd:ee:ff:11:22 | FAP-Lobby-1 | 2.4GHz | 54Mbps | Wrong PSK
```

**Now each log entry tells a complete story:**

- WHO connected (Station Mac)
- WHERE they connected (Physical AP)
- WHICH band they used (Band)
- HOW FAST they connected (Data Rate)
- WHAT happened (Message)

---

### Filtering — Focusing the Investigation

**Filter by Station MAC — investigating a specific client:**

```
Scenario: User "John" reports intermittent Wi-Fi drops
          John's device MAC: e4:a4:71:80:bc:27

Step 1: Add filter → Station Mac = e4:a4:71:80:bc:27
Step 2: Set time range to last 24 hours

Filtered results show ONLY John's device events:
  09:05:01 | Connected | e4:a4:71:80:bc:27 | FAP-Floor2-NE | 5GHz | 867Mbps
  09:05:01 | DHCP-Assigned | e4:a4:71:80:bc:27 | IP: 10.10.1.45
  10:32:17 | Disconnected | e4:a4:71:80:bc:27 | FAP-Floor2-NE | Reason: Deauth (Code 3)
  10:32:25 | Connected | e4:a4:71:80:bc:27 | FAP-Floor3-SW | 2.4GHz | 150Mbps ← roamed!
  10:35:01 | Disconnected | e4:a4:71:80:bc:27 | FAP-Floor3-SW | Reason: Deauth (Code 3)
  10:35:09 | Connected | e4:a4:71:80:bc:27 | FAP-Floor2-NE | 5GHz | 450Mbps ← roamed back!
  
Analysis:
  John's device is "sticky" — roaming between Floor2-NE and Floor3-SW repeatedly
  Signal threshold issues → device not roaming efficiently
  Each roam = brief disconnection → explains "intermittent drops"
  
Resolution:
  Enable 802.11r Fast BSS Transition for seamless roaming
  OR check AP Handoff threshold settings
  OR verify John's device Wi-Fi driver is updated
```

---

**Filter by Physical AP — investigating a specific AP:**

```
Scenario: Users near the conference room AP report poor connectivity
          AP: FAP-Conference-A

Step 1: Add filter → Physical AP = FAP-Conference-A
Step 2: Add column: Station Mac, Data Rate, Band
Step 3: Set time range to last hour

Filtered results:
  13:00:05 | Connected | e4:a4:71:80:bc:27 | FAP-Conference-A | 2.4GHz | 54Mbps
  13:00:12 | Connected | aa:bb:cc:dd:ee:ff | FAP-Conference-A | 2.4GHz | 54Mbps
  13:00:18 | Connected | 11:22:33:44:55:66 | FAP-Conference-A | 2.4GHz | 54Mbps
  13:05:22 | Auth-Fail  | cc:dd:ee:ff:11:22 | FAP-Conference-A | 2.4GHz | n/a
  13:05:45 | Auth-Fail  | cc:dd:ee:ff:11:22 | FAP-Conference-A | 2.4GHz | n/a
  
Analysis:
  ALL clients connecting at 54 Mbps on 2.4 GHz → performance issue
  54 Mbps = legacy rate → 802.11g behavior → very old
  These should be connecting at 300+ Mbps on 2.4 GHz n or much higher on 5 GHz
  
  Two possible causes:
  1. AP radio stuck in legacy mode → check AP profile radio settings
  2. Frequency handoff not working → clients not being steered to 5 GHz
  
  One auth failure (Code cc:dd:...) → possible incorrect PSK on one device
```

---

### Key Event Types to Know — What Each Means

```
Station-Connected:
  Client successfully associated and authenticated
  Healthy event — normal operation
  
Station-Disconnected:
  Client left the network (graceful disconnect or timeout)
  Check Reason Code to determine if graceful or forced
  
DHCP-Assigned / DHCP-Timeout:
  DHCP-Assigned = client got IP → normal
  DHCP-Timeout = client couldn't get IP → investigate DHCP server
  
Auth-Failed:
  Authentication rejected
  Sub-types:
    Wrong PSK → client has incorrect password
    RADIUS-Reject → RADIUS server denied → check RADIUS config
    4-way-Handshake-Timeout → network/signal quality issue
    
Rogue-AP-Detected:
  WIDS detected unknown AP → investigate
  
Deauth-Received:
  AP received deauthentication from client or network
  If source is legitimate client → normal
  If source is unknown → possible spoofed deauth attack
  
Station-Roamed:
  Client moved from one AP to another
  Check Data Rate before/after → verify roam improved connection
  Frequent roaming = sticky client problem → investigate handoff settings
  
Radio-Changed:
  DARRP changed the channel → expected if DARRP enabled
  If unexpected → investigate interference or DARRP profile settings
```

---

### Combining Columns and Filters — Advanced Investigation Technique

**Multi-filter investigation (client on specific AP):**

```
Filter 1: Station Mac = e4:a4:71:80:bc:27 (specific client)
Filter 2: Physical AP = FAP-Floor2-NE (specific AP)
Time range: Last 2 hours

Now see: Only events where THIS client was on THIS specific AP
→ Eliminate roaming noise → pure signal quality analysis
→ Identify if problem is with this AP specifically or follows the client everywhere
```

**Time-based correlation:**

```
Scenario: "Wi-Fi drops at exactly 10:30 every morning for 15 minutes"

Step 1: Filter Wi-Fi Events by time range 10:25-10:45 for multiple days
Step 2: Add Physical AP column → sort by AP
Step 3: Look for pattern:
  10:30:00 | Radio-Changed | FAP-Floor2-NE | DARRP channel change
  10:30:05 | Station-Disconnected | 47 clients disconnected from FAP-Floor2-NE
  → DARRP is running at 10:30 → changing channels → all clients briefly disconnected
  
Resolution:
  Configure DARRP schedule to avoid business hours
  Set darrp-optimize-schedules to run at 3 AM instead
```

---

### Log Export and External Analysis

The slide mentions the table is **filterable and the per-client log is exportable** (referenced on page 288):

For Wi-Fi Event Logs, you can also:

- **Export to CSV** for analysis in Excel/BI tools
- **Forward to FortiAnalyzer** for long-term retention, correlation, and advanced reporting
- **Forward to SIEM** via FortiGate's syslog forwarding (separate from FortiAP syslog profile)

**FortiAnalyzer integration:**

```
FortiGate sends all wireless logs to FortiAnalyzer
FortiAnalyzer provides:
  → Long-term retention (months/years vs. days on FortiGate)
  → Cross-device correlation (wireless events + firewall events)
  → Automated reports (weekly Wi-Fi health, client behavior trends)
  → IOC matching (compromised wireless clients flagged)
  → Compliance reports (PCI-DSS wireless audit evidence)
```

---

---

# 📊 Master Summary — Pages 299–300

```
┌───────<horizontalrule></horizontalrule><paragraph></paragraph>───────────────────────────────────────────────────────────────────┐
│         SYSLOG PROFILES & WI-FI EVENT LOGS — COMPLETE GUIDE             │
├─────────────────────────┬────────────────────────────────────────────────┤
│ TOPIC                   │ KEY FACTS                                      │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Syslog Profile          │ Sends AP logs DIRECTLY to external syslog      │
│                         │ Works with FortiGate AND FortiLAN Cloud mgmt   │
│                         │ Log types: Event logs + UTM logs               │
│                         │ Configured in: FortiAP Profile settings        │
│                         │ One profile → assignable to multiple AP profiles│
│                         │ Specify: log level + server IP                  │
│                         │ Port: default 514 (UDP) → CLI to change        │
│                         │ CLI: config wireless-controller syslog-profile  │
│                         │ Use case: SIEM compliance, FortiGate-offline    │
│                         │ continuity, FortiLAN Cloud local log retention  │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Log Levels              │ 0-Emergency → 7-Debug                         │
│                         │ Production: Warning (4) or Notice (5)          │
│                         │ Troubleshooting: Info (6) or Debug (7)         │
│                         │ ⚠️ Debug = massive volume → temp use only      │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Wi-Fi Event Logs        │ Location: Log & Report > Events > WiFi Events  │
│                         │ Scope: Entire controller — ALL APs + clients   │
│                         │ Provides: Historical wireless event timeline    │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Key Columns to Add      │ Band → which frequency band client used        │
│                         │ Data Rate → connection speed at event time     │
│                         │ Physical AP → which AP was involved            │
│                         │ Station Mac → which client MAC was involved    │
├─────────────────────────┼────────────────────────────────────────────────┤
│ Key Filters to Use      │ Station Mac → all events for specific client   │
│                         │ Physical AP → all events for specific AP       │
│                         │ Combine both → client on specific AP only      │
│                         │ Time range → isolate recurring time-based issues│
├─────────────────────────┼────────────────────────────────────────────────┤
│ Critical Event Types    │ Auth-Failed → wrong PSK/RADIUS reject          │
│                         │ DHCP-Timeout → DHCP server unreachable         │
│                         │ Deauth-Received → possible spoofed deauth      │
│                         │ Station-Roamed → verify data rate improved     │
│                         │ Radio-Changed → DARRP channel change           │
│                         │ Rogue-AP-Detected → investigate immediately    │
└─────────────────────────┴────────────────────────────────────────────────┘

⚠️  KEY OPERATIONAL RULES:
  1. Syslog profile logs go DIRECT from AP to syslog — parallel to FortiGate logs
  2. Default syslog port = 514 UDP — change via CLI only
  3. Debug log level = temporary troubleshooting ONLY — never leave in production
  4. WiFi Event Logs = whole-controller view; Per-AP Logs = single AP view
  5. ALWAYS add Band + Data Rate + Physical AP + Station Mac columns
  6. Filter by Station Mac to follow one client across entire deployment
  7. Filter by Physical AP to see all events on one AP in one view
  8. Data Rate of 54 Mbps on 5 GHz = red flag → legacy mode or band steering failure
  9. Frequent roaming events = sticky client problem → investigate handoff settings
  10. DARRP channel changes ap
```

---