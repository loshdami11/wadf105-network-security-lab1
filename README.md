# wadf105-network-security-lab1
# Part A
1. I shut down the icdfa firewall v1 completely.
  
   ![Firewall Adapter Settings](https://github.com/user-attachments/assets/49fff7c8-6ad7-4c14-ac7e-247987969041)
   

2.  I selected the icdfa settings and i choose network.

   

3. For Adapter 1 , i selected network adapter and set attached to NAT.

   ![Adapter 1 SELECTION](https://github.com/user-attachments/assets/c9b59fa4-a6b6-4586-89e6-9400640a9c39)
   

4. For Adapter 2 , i selected  network adapter and set internal network and selected icdfa-lan.

   ![Adapter 2 SELECTION](https://github.com/user-attachments/assets/61596180-19f9-46fd-9254-0765e37ebc13)

   

5. For Adapter 3 and Adapter 4, I disabled them.

   ![Adapter 3](https://github.com/user-attachments/assets/5dd022df-b206-49e5-8766-9d9503c91dfb)



   ![Adapter 4](https://github.com/user-attachments/assets/c79711ba-f496-4ffa-9d6b-00b4ca8a49ec)


6. I selected icdfa-nslab client-v1 and i opened settings and selected network

7. For Adapter 1, I selected Internal Network and enter ICDFA-LAN. Confirm Cable Connected is selected.

   ![Adapter 1](https://github.com/user-attachments/assets/9125dc40-316b-44b4-8b2d-3e53c744326f)


8. I disabled the remaining Ubuntu adapters and save all settings.


# Part B

9. I started icdfa-nslab-firewall-v1 and waited for the OPNsense console menu.

10. I confirmed that one interface is marked WAN and the second is marked LAN.

11. Confirmed that the LAN address was set to `10.10.10.1/24`.

12. Re-assigned interfaces (`WAN -> em0`, `LAN -> em1`) and verified LAN addressing.

13. Enabled the DHCP server on LAN with an IP range of `10.10.10.100` to `10.10.10.200`.

<img width="621" height="385" alt="image" src="https://github.com/user-attachments/assets/98aba972-af38-4553-b70f-76912e9237ac" />
">

14. I do not configure a gateway on the LAN interface because the WAN interface obtained its address automatically through DHCP.

# Part C 

15. I started icdfa-nslab-client-v1 only after icdfa-nslab-firewall-v1 was ready.

 ## Part C: Verify Ubuntu Addressing

16. Verified network auto-configuration on `icdfa-nslab-client-v1`:
   - **Interface:** `enp0s3`
   - **IPv4 Address:** `10.10.10.131/24`
   - **Default Gateway:** `10.10.10.1`
   - **DNS Server:** `10.10.10.1` (`icdfa.test`)

![Ubuntu Network Verification Evidence ](https://github.com/user-attachments/assets/f0628e7f-90bf-4398-9472-477ae41c8481)


## Part D
1. Ubuntu can reach the firewall LAN interface
  
 <img width="360" height="141" alt="image" src="https://github.com/user-attachments/assets/9ce68605-9e9c-492f-81e7-b4f5603fb46e" />


 2. Routing and outbound NAT are working
    
    <img width="388" height="135" alt="image" src="https://github.com/user-attachments/assets/8133829f-0f89-4e03-8b95-ee4e18715055" />


  3. DNS name resolution is working

     <img width="301" height="44" alt="image" src="https://github.com/user-attachments/assets/900f775b-ff91-44b7-b7f7-fd8a57df2fbd" />

  4. TCP, TLS and web connectivity are working

     <img width="409" height="122" alt="image" src="https://github.com/user-attachments/assets/98699561-a17f-40ff-b7cd-8da70a3c72a1" />


## Part E

17. I opened Wireshark on Ubuntu.

18. I selected the active Ethernet interface carrying the 10.10.10.x address and begin capture

19. In Terminal I  ping -c 4 10.10.10.1 and getent hosts opnsense.org.

20. I stopped the capture after the commands finish.

    <img width="326" height="287" alt="image" src="https://github.com/user-attachments/assets/6b511cc1-3e47-4c87-9745-1d25f6974518" />


21. arp requests

    <img width="470" height="284" alt="image" src="https://github.com/user-attachments/assets/be735045-130b-439d-9565-d323632c19f1" />

ARP Request (Frame 14): Sent as a broadcast by the Ubuntu client (10.10.10.131) asking "Who has 10.10.10.1? Tell 10.10.10.131" to map the IP address of the firewall gateway to its MAC address.

ARP Reply (Frame 15): Sent as a unicast response by the OPNsense firewall answering "10.10.10.1 is at 08:00:27:41:b0:10", providing its physical MAC address so layer 2 frame encapsulation can occur

icmp requests

   <img width="470" height="301" alt="image" src="https://github.com/user-attachments/assets/c4dcfaf5-243e-441a-9291-3074bb15ce7d" />

   ICMP Echo Request (Frame 6): Sent by the Ubuntu client (10.10.10.131) to the default gateway (10.10.10.1) to test layer 3 network reachability

   ICMP Echo Reply (Frame 7): Returned immediately by the OPNsense firewall (10.10.10.1) back to the Ubuntu client (10.10.10.131), confirming bidirectional IP connectivity with 0% packet loss


Dns requests
 <img width="481" height="353" alt="image" src="https://github.com/user-attachments/assets/142f9b43-c10d-4081-87a8-cb427131f299" />

DNS Query (Frame 19): Sent over UDP from the Ubuntu client (10.10.10.131) to the configured DNS server on OPNsense (10.10.10.1) requesting the domain address resolution (AAAA record for opnsense.org).

DNS Response (Frame 20): Returned by the OPNsense firewall (10.10.10.1) back to the client (10.10.10.131), providing the resolved IP information to complete domain name resolution.


Evidence to submit

## E7: Default Gateway Explanation

The client virtual machine `icdfa-nslab-client-v1` uses `10.10.10.1` as its default gateway because it resides on an isolated internal network (`ICDFA-LAN`) without direct external network connectivity. To communicate with endpoints outside its local `10.10.10.0/24` subnet—such as internet destinations—traffic must be directed to a layer 3 routing device that bridges the internal network to upstream interfaces. The OPNsense firewall's LAN interface is configured with static IP `10.10.10.1`, serving as the default gateway that receives out-of-subnet packets from local hosts, applies Network Address Translation (NAT), and routes traffic through its WAN interface to external destinations.


29. What is the difference between the OPNsense WAN and LAN interfaces?
The WAN (Wide Area Network) interface connects OPNsense to the untrusted external network or VirtualBox NAT gateway to reach the internet, using DHCP to obtain an upstream IP address. The LAN (Local Area Network) interface connects to the isolated private virtual network (10.10.10.1/24), providing local services such as DHCP leasing and acting as the default gateway for internal client VMs.


30. Why must both internal adapters use the same VirtualBox network name?
VirtualBox isolates internal networks based on their exact string name. Both the OPNsense internal adapter and the Ubuntu client adapter must share the exact same network name (ICDFA-LAN) so VirtualBox places them on the same virtual Layer 2 software switch, allowing them to broadcast frames and communicate with each other.


31. What information does the default route provide to Ubuntu?
The default route provides Ubuntu with the IP address of its local gateway (10.10.10.1) and the interface to use (enp0s3) for forwarding any IP traffic whose destination subnet is outside the local 10.10.10.0/24 network.

32. Which packet exchange allows Ubuntu to learn the firewall MAC address?
The Address Resolution Protocol (ARP) exchange. Specifically, Ubuntu broadcasts an ARP Request querying "Who has 10.10.10.1?", and OPNsense replies via unicast with an ARP Reply containing its hardware MAC address (08:00:27:41:b0:10).

33. Why does a successful ping to 1.1.1.1 not automatically prove that DNS is working?
Pinging 1.1.1.1 tests connectivity directly using a numerical IP address, which evaluates Layer 3 routing and outbound NAT rules without requiring hostname resolution. Domain Name System (DNS) operates on a separate protocol (UDP/TCP port 53) to translate domain names (like opnsense.org) into IP addresses; therefore traffic can successfully route to an IP address even if DNS servers or resolution services are down.


   






    




