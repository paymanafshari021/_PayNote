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