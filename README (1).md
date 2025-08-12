# Multi-PE-MPLS-VPN-lab-with-Hub-Spoke-DR-Resilience-and-internet-breakout-Part-2


Hi chat GPT. 

I am aware you remember my Multi-PE-MPLS-VPN-lab-with-Hub-Spoke-DR-Resilience-and-internet-breakout-Part-1

This is part two and i am sharing my lap outpus in the form of readme file explaining every output with output snip below 

in this part of my lab i want to show my lab output in five instances 

1) HUb to DR without backdoor link behavior

2) Hub to DR with backdoor link behavior

3)  Hub and DR behavior post creating sham link between PE1 To PE5

4) Spoke to Hub reachability via DR when Hub to PE1 link down 

5) traffic fall back from spoke to Hub rechability via DR when Hub to PE down  to Traffic from hub to spoke via PE1 post Hub to PE1 link restoration 

Part -2 Topology :
=================

<img width="1759" height="909" alt="Image" src="https://github.com/user-attachments/assets/6f89e45f-491e-4f34-811a-7134b8ea1274" />

Instance-1 : HUb to DR without backdoor link behavior :-
======================================

In this instance when link between HUB and PE1 goes down then my HUB becomes isolated and Hub to spoke communication goes down causing huge issue and communication blockage and business operations will get affected 

Topology :
------------
<img width="1365" height="864" alt="Image" src="https://github.com/user-attachments/assets/920cb5b7-4ea8-4800-a5bf-9fe79ef5ed0a" />

Hub and DR output without back door link :
-------------------------------------------------

Without backdoor link between HUB to DR the Hub routes to DR and DR routs to hub propogatins via PE1 Mpls path and DR roues in hub will be read as inter roues OIA and same in hub the DR routes are leared as OIA

Note : When PE1 to HUB link goes down entire orgation coommunication will go dpwn 

<img width="1824" height="607" alt="Image" src="https://github.com/user-attachments/assets/edfce21a-5fb6-4645-ad72-0c2d538a6127" />


Instance-2 : HUb to DR with backdoor link behavior :-
======================================

The need for introducing backdoor link is when hub to PE1 fails i want redundency and i want my spoke to communicate with my hub even when my hub to PE1 link goes down 

So i implemented to backdoor link and connected my Hub and DR using backdoor link in real time like leased link or point to point link 

Topology :
------------

<img width="1512" height="889" alt="Image" src="https://github.com/user-attachments/assets/3f2b9e37-45a3-4ca4-90b2-432deaff83d3" />

Hub and DR output without back door link :
-------------------------------------------------

When backdoor link become live my hub routes in DR and DR routs in hub are leared like O which LSA 1 and traffic between HUB and DR started flowing via backdoor link instead via Mpls path 

<img width="1872" height="656" alt="Image" src="https://github.com/user-attachments/assets/83701d2b-34a7-405b-947b-fd4441e3198b" />

As leased line are costly my bandwidth on leased line is less and this leased line need to come into action only when Hub to PE1 goes down, so to over come this issue 

I increased the OSPF cost on leased link but yet still traffic between HUB and DR is via Backdoor because LSA 1 is given first priority rather then cost 

<img width="1847" height="730" alt="Image" src="https://github.com/user-attachments/assets/866faf6a-d2db-4e14-85b8-5f3d29e98012" />

Heance to over come this issue and to make sure my traffic between hub and DR flow through Mpls infra i created SHAM LINK between PE1 to PE2 


Instance-3 : Hub and DR behavior post creating sham link between PE1 To PE5 :-
======================================================

Post creating sham link between PE1 and PE5 the traffic between HUB and DR started to flow via mpls infra because sham link is like extension of area 1 from PE1 to PE5 it manupultes the control plance and Hub and DR thinks this there is direct area 1 connection and as cost on Back door link is set to maximum 65535 traffic flow from mpls infra 

Below are the images of hub to DE reachability roues and trace route supporting traffic is via mpls infra 

<img width="1873" height="818" alt="Image" src="https://github.com/user-attachments/assets/7e4619e5-6a6d-45b5-a354-726432426ad9" />

<img width="1849" height="873" alt="Image" src="https://github.com/user-attachments/assets/2c93986f-f762-4095-a03d-6cc927b49cc3" />

Instance-4 : Spoke to Hub reachability via DR when Hub to PE1 link down 
=============================================

In this instance the link between hub and PE1 goes down and traffic between hub to spoke will propogate via DR and reachs hub with the help of Backdoor link, below are the output of all spokes and hub  when hub to PE1link goes down

Topology :
------------

<img width="1688" height="881" alt="Image" src="https://github.com/user-attachments/assets/5541efb7-080e-4909-91a0-1a2c45096ad9" />

Hub to all spoke reachability via DR and backdoor link when Hub to PE1 link down :
---------------------------------------------------------------------------------------------

<img width="919" height="493" alt="Image" src="https://github.com/user-attachments/assets/740d9298-0d54-44f8-8eb2-46b74ede0381" />

Spk1 to Hub reachability ping, Trace route output via DR when Hub to PE1 link is down :
---------------------------------------------------------------------------------------------

<img width="794" height="575" alt="Image" src="https://github.com/user-attachments/assets/b9236e36-1e12-4a8f-9091-991f3f65ee57" />

Spk2 to Hub reachability ping, Trace route output via DR when Hub to PE1 link is down :
---------------------------------------------------------------------------------------------

<img width="758" height="452" alt="Image" src="https://github.com/user-attachments/assets/40366d18-0ff5-4d3c-b36d-ce7dbbf5064e" />

Spk3 to Hub reachability ping, Trace route output via DR when Hub to PE1 link is down :
---------------------------------------------------------------------------------------------

<img width="811" height="374" alt="Image" src="https://github.com/user-attachments/assets/4b815983-f8fa-4c27-b465-b69a1031272a" />

HUB to all spoke trace route when Hub to PE1 link down :
---------------------------------------------------------------------------------------------

<img width="852" height="555" alt="Image" src="https://github.com/user-attachments/assets/96f03f65-8d41-4b9e-9526-fad938b3c8a1" />

all spoke routes reached hub via DR due to HUB to P1 link down :
---------------------------------------------------------------------------------------------

<img width="1121" height="993" alt="Image" src="https://github.com/user-attachments/assets/dff319c7-3915-437d-9a90-9086143828e3" />

Hub and DR learning routes via backdoor due to HUB to PE1 link down:
---------------------------------------------------------------------------------------------

<img width="1860" height="802" alt="Image" src="https://github.com/user-attachments/assets/f279db5b-daba-423e-ab24-c715a41f4205" />

Instance-5 :traffic fall back from spoke to Hub reachability via DR when Hub to PE down  to Traffic from hub to spoke via PE1 post Hub to PE1 link 
=============================================================================================
restoration 
=======

In this instance the link between hub and PE1 restore and traffic between hub to spoke will fallback via PE1, below is the topology and output supporting snaps 

Topology :
------------

<img width="1684" height="892" alt="Image" src="https://github.com/user-attachments/assets/003973b2-3ca3-4750-85dd-6e1eedee41fe" />

HUB To All Spoke Reachability via P1 post Hub to PE1 restored :
-----------------------------------------------------------------------

<img width="797" height="506" alt="Image" src="https://github.com/user-attachments/assets/e539e78b-5b24-434e-a306-2270d91b18d0" />

HUB To All Spoke Traceroute via P1 post Hub to PE1 restored :
-----------------------------------------------------------------------

<img width="800" height="538" alt="Image" src="https://github.com/user-attachments/assets/60185258-743b-4691-b5b9-9350ea8795b0" />

Hub Routes post Hub to PE1 link restored :
-----------------------------------------------------------------------

<img width="774" height="814" alt="Image" src="https://github.com/user-attachments/assets/c398245f-ff08-4c63-b2f9-93950860c9f6" /

Ping and trace route logs to hub from PE1 post Hub to PE1 link got restored :
-----------------------------------------------------------------------

<img width="1845" height="1031" alt="Image" src="https://github.com/user-attachments/assets/d2330847-6705-492b-b790-833d88a1abcb" />

