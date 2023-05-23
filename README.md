# Building-a-Small-Business-Environment
The MSP I work for has been hired to build a small business environment.

The client has requested the following:

- a secure network
- an internal Windows Domain
- an internal Microsoft IIS webserver
- an internal Windows 10 workstation
- a public webserver
- a public FTP server
- a LAN network on 10.128.0.0/24
- a DMZ network on 10.128.10.0/24
- a GUEST network on 10.128.99.0/24

Stage1 : Network Setup : Topology

Our topology will start with the WAN-CLOUD a.k.a. the internet, our WAN-SWITCH, our FortiGate which is our firewall,
a LAN-SWITCH, DMZ-SWITCH, and our Windows 10 box.

![TopologyOpen1](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/9a5ba700-b2a0-4dfa-a7ec-ce4759c169f2)

Next we will plug everything in using a RJ-45 cable. Note that with firewalls different ports offer different services.
For our FortiGate1 port 1 is for the WAN, port 2 is for LAN, port 3 is for guests, and port 4 is for the DMZ. Switches
do not have the same specifications.

![TopologyFirewall](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/d5fc1af5-e6ac-4eb4-aafb-dfd722c1c969)
With everything plugged in out topology should look like this:
![Topology Pt1 End](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/5aebaec5-ae97-446a-a12c-861016d67871)
