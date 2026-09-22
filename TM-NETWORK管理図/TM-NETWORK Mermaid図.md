
```mermaid
graph TD
    Internet[インターネット<br/>auひかり]
    ONU[ONU]
    HGW[HGW<br/>192.168.0.1]
    XR500[XR500<br/>192.168.1.1]

	Internet --> ONU --> HGW --> XR500
 
	subgraph 有線LAN["有線LAN (192.168.1.x)"]
	 NAS1["NAS1 (休止中)<br/>192.168.1.3"]
	 NAS2["NAS2<br/>192.168.1.2"]
	 PC["メインPC<br/>192.168.1.4"]
  end 
  
	subgraph Wi-Fi2.4GHz["Wi-fi2.4GHz"]
	 Printer["プリンタ(休止中)"]
  end  

	subgraph Wi-Fi5GHz["Wi-fi5GHz"]
	 sub["サブPC<br/>192.168.1.5"]
	 Makura["まくらトップ<br/>192.168.1.6"]
	 PS4[PS4]
	 Phone1["TorqueG07<br/>192.168.1.7"]
	 Phone2["Torque5G<br/>192.168.1.10"]
  end
	
	 XR500 -->|LAN1| NAS1
	 XR500 -->|LAN2| NAS2
	 XR500 -->|LAN3| PC
	 
	 XR500 -.-> Printer
	 
	 XR500 -.-> sub
	 XR500 -.-> Makura
	 XR500 -.-> PS4
	 XR500 -.-> Phone1
	 XR500 -.-> Phone2
	 
 ``` 