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
+ Best practice: 3–5 SSIDs per radio maximum in most deployments
