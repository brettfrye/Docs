.. meta::
   :description: Site2Cloud (Aviatrix Gateway - Barracuda Ng Firewall)
   :keywords: Barracuda, aviatrix, site2cloud



=====================================================================
Aviatrix Gateway to Barracuda NG
=====================================================================

Overview
--------
This document describes how to configure an IPsec tunnel between an Aviatrix Gateway and a Barracuda NG firewall using Aviatrix Site2Cloud.

Add a Site2Cloud tunnel in Aviatrix Controller
-----------------------------------------------
1. Login to your Aviatrix Controller.
2. Select the Site2Cloud navigation item on the left navigation bar.
3. Click on `+ Add New` near the top of the `Site2Cloud` tab.
4. Under `Add a New Connection`, enter the following:

  +-------------------------------+------------------------------------------+
  | Field                         | Expected Value                           |
  +===============================+==========================================+
  | VPC ID / VNet Name            | Select the VPC where this tunnel will    |
  |                               | terminate in the cloud.                  |
  +-------------------------------+------------------------------------------+
  | Connection Type               | `Unmapped` unless there is an            |
  |                               | overlapping CIDR block.                  |
  +-------------------------------+------------------------------------------+
  | Connection Name               | Name this connection.  This connection   |
  |                               | represents the connectivity to the       |
  |                               | edge device.                             |
  +-------------------------------+------------------------------------------+
  | Remote Gateway Type           | `Generic`                                |
  +-------------------------------+------------------------------------------+
  | Tunnel Type                   | `UDP`                                    |
  +-------------------------------+------------------------------------------+
  | Algorithms                    | Unchecked                                |
  +-------------------------------+------------------------------------------+
  | Encryption over ExpressRoute/ | Unchecked                                |
  | DirectConnect                 |                                          |
  +-------------------------------+------------------------------------------+
  | Enable HA                     | Unchecked                                |
  +-------------------------------+------------------------------------------+
  | Primary Cloud Gateway         | Select the Gateway where the tunnel will |
  |                               | terminate in this VPC.                   |
  +-------------------------------+------------------------------------------+
  | Remote Gateway IP Address     | IP address of the Barracuda device.        |
  +-------------------------------+------------------------------------------+
  | Pre-shared Key                | Optional.  Enter the pre-shared key for  |
  |                               | this connection.  If nothing is entered  |
  |                               | one will be generated for you.           |
  +-------------------------------+------------------------------------------+
  | Remote Subnet                 | Enter the CIDR representing the network  |
  |                               | behind the edge device that this tunnel  |
  |                               | supports.                                |
  +-------------------------------+------------------------------------------+
  | Local Subnet                  | The CIDR block that should be advertised |
  |                               | on pfSense for the cloud network (will   |
  |                               | default to the VPC CIDR block)           |
  +-------------------------------+------------------------------------------+

5. Click `OK`

Create an IPsec tunnel in Barracuda
---------------------------------

Documentation
Be sure to reference your Barracuda Documentation to make sure that your firewall is ready to accept VPN Connections and is properly configured to start the process

1. Log into your NG via Ng Admin and go to your Configuration tree.
2. Go to CONFIGURATION > Configuration Tree > Box > Virtual Servers > your virtual server > Assigned Services > VPN-Service > Site to Site.
3. Click the IPSEC IKEv1 Tunnels tab
4. Right-click the table and select New IPSec IKEv1 tunnel. The IPsec Tunnel window opens
5. Select the Phase 1 settings according to the configuration file you downloaded    *General Information*
 |imageExamplePhase1-Step5|

6. Select the Phase 2 settings according to the configuration file you downloaded
    a.	Note you must enable PFS on the NG
|imageExamplePhase2-Step6|

7.	Click the Local Networks tab and configure the following settings:
    a.	Initiates Tunnel– Select Yes (active IKE) for the Barracuda CloudGen Firewall to initiate the VPN Tunnel.
    b.	Local IKE Gateway – Enter the IPv4 or IPv6 address the VPN service is listening on. If you are using a dynamic WAN IP address, enter 0.0.0.0 , or ::0.
    c.	ID-type – Select the IPsec ID-type. For more information, see IPsec IKEv1 Tunnel Settings. – You will leave this as the default setting
    d.	Network Address – Add the local networks you want to reach through the VPN tunnel, and click Add. – These are the remote AWS networks from your configuration file
|imageExampleStep7|
 
8.	Click the Remote Networks tab, and configure the following settings:
   a.	Remote IKE Gateway
      1. You have two options to configure the remote IKE Gateway:
          b.	ID-type – Select the IPsec ID-type. For more information, see IPsec IKEv1 Tunnel Settings. – You will leave this at the defaults 
          c.	Network Address – Add the IP address of the remote network, and enable Advertise Route if you want to propagate it via RIP, OSPF, or BGP. (e.g., 10.0.81.0/24). Enter the address and then click Add.
|imageExampleStep8|
  
 9.	Click the Identify and Peer Identification tab, and enter the shared passphrase in the Shared Secret field. The passphrase may not contain the hash (#) character due to limits in Barracuda.  
|imageExampleStep9|
 
 10. Now you are going to Click on OK and then you want to send your changes and Activate them on the Barracuda NG Firewall.
 11. Test your connection by riunning a ping test from an onsite system to a system in the cloud subnet.
 |imageExamplePingTest| 
 
 
.. |imageExamplePhase1-Step5| image:: Site2Cloud_Barracuda_media/Phase1-Step5.png
.. |imageExamplePhase2-Step6| image:: Site2Cloud_Barracuda_media/Phase2-Step6.png 
.. |imageExampleStep7| image:: Site2Cloud_Barracuda_media/Step 7.png
.. |imageExampleStep8| image:: Site2Cloud_Barracuda_media/Step 8.png
.. |imageExampleStep9| image:: Site2Cloud_Barracuda_media/Step 9.png
.. |imageExamplePingTest| image:: Site2Cloud_Barracuda_media/pingtest.png
