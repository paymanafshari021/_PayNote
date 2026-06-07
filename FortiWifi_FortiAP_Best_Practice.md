## FortiWifi_FortiAP
+ Set based on expected concurrent users — not maximum possible users
+ A general rule: 25–30 clients per radio for good performance
+ For IoT networks: set a tight limit to prevent unauthorized device sprawl
+ WiFi & Switch Controller > SSIDs > [Select SSID]
Max Clients: [enter number, e.g., 30]
  → 0 = unlimited (default)
+ Disable SSID Broadcast (Hidden SSID)
+ Hidden SSIDs provide very limited real security. Any wireless scanning tool (Wireshark, Kismet, NetSpot) can detect hidden SSIDs through:
    + Passive monitoring of probe responses when a legitimate client connects
    + Active probing — tools send directed probe requests using common SSID names
    + Analyzing beacon frames — the AP still broadcasts, just without the SSID name field
+ Always combine with strong encryption (WPA3) and authentication (802.1X).
+ Beacon frames are sent by APs approximately 10 times per second to announce their presence and capabilities. Every wireless device in range receives these frames — they're how your phone "sees" available Wi-Fi networks.
+ When a feature is described as "Fortinet-proprietary IE," it means Fortinet has created its own custom data fields to pass specialized information between devices. Because these fields aren't part of the standard, universal networking protocols, only Fortinet devices know how to read and use them.
+ Beacon advertising Name,Model,Serial number
+ Adding a new SSID to the group → automatically applies to ALL AP profiles referencing that group. No need to edit each profile individually.
+ Important operational limit: Most FortiAP models support a maximum of 8 SSIDs per radio. When using SSID groups, ensure the total number of SSIDs in the group does not exceed this limit.
+ 3–5 SSIDs per radio maximum in most deployments
+ The local-bridging enable CLI Command - The key CLI parameter that enables Bridge Mode VLAN behavior:
  + set local-bridging enable
    + This tells FortiAP to:
      1. Accept wireless client traffic
      2. Apply the appropriate VLAN tag based on SSID/RADIUS assignment
      3. Forward the tagged traffic directly onto the wired interface (802.1Q trunk)
      4. NOT send data traffic through CAPWAP to FortiGate
        + Without this setting → traffic goes through CAPWAP to FortiGate (Tunnel Mode behavior).
+ Change management VLAN from VLAN 1 to a dedicated management VLAN (e.g., VLAN 99). VLAN 1 is the default on most switches and is a common attack target.
+ Dynamic VLANs - Three IETF RADIUS Attributes
  + Tunnel-Type = VLAN 
  + Tunnel-Medium-Type = IEEE 802
  + Tunnel-Private-Group-ID = [VLAN ID or name]
+ Default VLAN 100 = fallback if RADIUS sends no VLAN info
+ The default VLAN is your safety net AND a diagnostic tool. If many users are landing on the default VLAN unexpectedly, your RADIUS attribute configuration likely has an error.
+ Before enabling dynamic-vlan:
  + All possible VLAN sub-interfaces created on FortiGate
  + Each VLAN has IP address configured
  + Each VLAN has DHCP server or relay configured
  + Each VLAN has at least one firewall policy
  + RADIUS server configured to return all 3 IETF attributes
  + Test with a known user and verify correct VLAN assignment
  + Default VLAN configured as fallback
  + Default VLAN firewall policy and DHCP configured
+ VLAN Assignment by Name Tag
  - Each name can map to **1 VLAN ID** (one-to-one) OR up to **8 VLAN IDs** (one-to-many)
  - When multiple VLAN IDs are assigned per name → **round-robin** selection
+ For the onboarding VLAN to work, **Device Detection must be enabled**
+ What device detection collects:
  - MAC OUI: First 3 bytes identify manufacturer → 70:4C:A5 = Fortinet (FortiAP)
+ **Why short lease time on onboarding VLAN?**
  - Device gets onboarding IP → NAC evaluates → moves to target VLAN → needs new IP