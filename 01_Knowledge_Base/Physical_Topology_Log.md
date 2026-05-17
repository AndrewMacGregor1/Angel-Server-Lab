## 1. Site Survey & The "Basement Problem"

This is basically just keeping track of why I had to run 100ft of cable in the first place.

- **The Issue:** The old coax ports in the walls are dead, and the wiring is complicated.
    
- **The Gap:** My main internet comes in through the basement, but my server and my desk are all the way up on the third floor. I also just want to be able to admire my beautiful lab from my bedroom.
    
- **The Fix:** I ran a 100ft Cat6 cable to act as the "Backbone Trunk." Instead of just plugging into the server, it now connects the basement gateway to my Cisco switch upstairs so I can handle all my VLANs.

## 2. The Gear (Layer 1)

These are the actual physical boxes making the lab work right now.

| **Component** | **What it is**               | **What it does**                                              |
| ------------- | ---------------------------- | ------------------------------------------------------------- |
| **Modem**     | Cox Panoramic (Bridge Mode)  | Just passes the raw internet signal to my router.             |
| **Router**    | Ubiquiti Cloud Gateway Ultra | The "brain"—handles all my firewall rules and VLANs.          |
| **The Cable** | 100ft Cat6 Patch Cable       | The main line running from the basement to the 3rd floor.     |
| **Switch**    | Cisco Catalyst 2960          | My upstairs "distributor" for the server and AP.              |
| **APs**       | 2x Ubiquiti nanoHD           | Gives me Wi-Fi coverage for both my regular and VPN networks. |
| **Server**    | Dell OptiPlex 9020 SFF       | The actual workhorse running all my LXCs and VMs.             |
|               |                              |                                                               |

## 3. How I Ran the Cables

I had to be pretty specific with this run so it didn't look like a mess or become a trip hazard.

1. **Start:** Plugs into **Port 1** of the Ubiquiti Gateway down in the basement.
    
2. **The Climb:** I ran it across the kitchen ceiling and through the service hole I found behind the fridge.
    
3. **The End Point:** It terminates into **Gigabit Port 0/1** on the Cisco switch upstairs.
    
4. **Final Hops:** * **Port f0/1:** A short cable goes right into the Dell OptiPlex.
    
    - **Port g0/2:** This powers the upstairs AP so I get a good signal in my room.
        

## 4. Maintenance & "Is it working?"

Just a log of when I added stuff and confirmed it wasn't broken.

- **2026-03-15:** Got the Ubiquiti Gateway online. Confirmed it was pulling an IP and everything was reachable.
    
- **2026-03-16:** Hooked up the Cisco 2960. Verified that "Trunking" is working, which lets both VLAN 1 and VLAN 10 travel over that 100ft cable.
    
- **Current Status:** Everything is hitting high speeds. The 100ft run is well under the limit for Cat6, so no signal lag.