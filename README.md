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

# Stage1 : Network Setup : Topology

Our topology will start with the WAN-CLOUD a.k.a. the internet, our WAN-SWITCH, our FortiGate which is our firewall,
a LAN-SWITCH, DMZ-SWITCH, and our Windows 10 box.

![TopologyOpen1](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/9a5ba700-b2a0-4dfa-a7ec-ce4759c169f2)

Next we will plug everything in using a RJ-45 cable. Note that with firewalls different ports offer different services.
For our FortiGate1 port 1 is for the WAN, port 2 is for LAN, port 3 is for guests, and port 4 is for the DMZ. Switches
do not have the same specifications.

![TopologyFirewall](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/d5fc1af5-e6ac-4eb4-aafb-dfd722c1c969)

With everything plugged in out topology should look like this:

![Topology Pt1 End](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/5aebaec5-ae97-446a-a12c-861016d67871)

# Stage1 : Network Setup : Configuring the LAN Network

Open up the switch console and log in using the default credentials.
### username = admin
### password = none

After loging in you will be prompted to create your own password.

Now we will configure the LAN-Switches settings that we were given to by our senior engineer.
Run the following commands:

### conf sys int
###   edit port2
###    set allowaccess ping http https ssh
###    set ip 10.128.0.1/24
### end
 
 ![LAN settings](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/a641555b-ec91-4bbe-8373-d2c46eaeddfc)
 
 verify that your configuration is correct by running:
 
 ### show sys int port2
 
 We will now enable DHCP and the LAN Network, our scope is from 10.128.0.[100-199].
 we will run:
 
###  conf sys dhcp server
###      edit 1
###          set default-gateway 10.128.0.1
###          set netmask 255.255.255.0
###          set interface port2
###          config ip-range
###              edit 1
###                  set start-ip 10.128.0.100
###                  set end-ip 10.128.0.199
###              next
###          end
###      next
###  end
  
  Verify that the configuration is correct by running:
  
###  show sys dhcp server 1
  
  ![DHCP check](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/b3085430-27c8-4c78-a659-1ed9e99b2a5d)
  
 # Stage1 : Network Setup : Add a Win10 workstation

After spinning up the Windows virtual machine and logging in we will open the command prompt and make sure that our ip,
gateway, and DHCP servers are correct by running:

### ipconfig /all

![ipconfigcheck](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/90d6eab2-fa7b-4855-9777-f2de1f5fd190)

along with the check we will ping:
LAN - 10.128.0.1
WAN - 8.8.8.8
DNS - google.com

Our results are that we can ping the LAN with no problem. The WAN and DNS are show as failed or timed out since we haven't
configured the settings.


# Stage1 : Network Setup : Connect to the firewall GUI

Next we will log into our firewallwall GUI by typing its address into our browser of choice.
We will use the same credentials we used to setup our firewall.

Go into the system settings and make changes according to your project.
We changed hostnames, timezones, local interfaces. interface lists, idle timeout, and aut file system check.
Back up the configuration and reboot the firewall.
 
 Stage1 : Network Setup : Complete the network setup
 
 I will not go into too much detail in all the configurations we made with the firewall so I will sum it up. We reconnected to the gui. 
 Configured the WAN, LAN, GUEST, and DMZ ports. Enabled DNS, Configured the firewall system DNS. Configured Network DNS for LAN, GUEST,
 and DMZ, Created service objects for LAN, and DMZ. Then we configured firewall rulesfor all the ports. And finally we backed up 
 the configuration.
 
 After all the configurations we re-ran the ping for WAN, and DNS. The WAN came back as successful but the DNS did not. After troubleshooting
 we found that the configurations were correct, all we had to do was re-run ipconfig /release and ipconfig /renew. Everything worked properly
 and we were able to connect to the cloud.
 
 
 

 
 

