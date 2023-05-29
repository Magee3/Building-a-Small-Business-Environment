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
 username = admin
 password = none

After loging in you will be prompted to create your own password.

Now we will configure the LAN-Switches settings that we were given to by our senior engineer.
Run the following commands:

 conf sys int
     edit port2
      set allowaccess ping http https ssh
      set ip 10.128.0.1/24
 end
 
 ![LAN settings](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/a641555b-ec91-4bbe-8373-d2c46eaeddfc)
 
 verify that your configuration is correct by running:
 
 ### show sys int port2
 
 We will now enable DHCP and the LAN Network, our scope is from 10.128.0.[100-199].
 we will run:
 
 conf sys dhcp server
       edit 1
          set default-gateway 10.128.0.1
          set netmask 255.255.255.0
           set interface port2
           config ip-range
              edit 1
                  set start-ip 10.128.0.100
                   set end-ip 10.128.0.199
              next
          end
       next
  end
  
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
 
 Lastly we connected to our firewall GUI to configure traffic rules for each device.
 
 # Stage2 : Domain Setup : Topology
 
 For stage to we will add and setup our Domain controller.
 
 ![AddingDomainTopology](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/d2177eb6-0206-41e1-b987-e197c8a13a44)
 
 ### Stage2 : Domain Setup : Prepare a Win2012r2 server
 
 We log into the Win server using default credentials and changing it to something more secured. We will use the following setup our network adapter.
 
   ip address: 10.128.0.10
  subnet mask: 255.255.255.0
  default gateway: 10.128.0.1
  
  DNS1: 127.0.0.1
  DNS2: 10.128.0.1

To change adapter settings go into control panel --> Network and Internet --> Network and Sharing Center --> Change adapter settings --> Right click your
adapter and select properties --> IPv4 settings.

The final window should look something like this:
![DCproperties](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/64385107-acef-4cb6-b3ed-a7362b447707)

To test our connections we will ping the following targets:
LAN-SWITCH: 10.128.0.1
WAN: 8.8.8.8
DNS: google.com

All the pings have came back successfull we will sync our time with the switches.
Right click the time on the bottom right of the task bar and select Adjust Date and Time.
Change the time zone to the same as the switch.
Afterwards click internet time and type in the switches ip and hit update now.

![sync time](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/07b77182-68dd-4de5-a011-0fc6d500260e)

It may not work the first time give it about 3-5 tries before troubleshooting.

Next we will change our hostname

In server manage go into local servers and click the computer name.
change the computer description to: DC
hit apply then change.

![hostname change](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/78d0f8be-1160-4a06-9e67-9011e4dc0a44)

### Stage2 : Domain Setup : Install Active Directory

Installing active directory to a Windows web server is really easy.
Go into server manager --> manage --> Add roles and features --> Active Directory

![011814_0709_buildingyou12](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/b633bb8e-507c-4d92-9774-2cc2b1a9bef4)

Keep the default settings until you reacht the server roles tab. Check "Active Directory Domain Services" and continue with the installation.
After installation has finished there should be a notification in the server manager asking you to promote the server to the Domain Controller
select "yes". If you do not get the notification you may have to refresh the page.

![011814_0709_buildingyou12](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/ae1dabc5-1c7a-450f-bbf1-5ddc16124b84)

Select the "root forest" option and name your root folder

![adname](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/2cf39276-7f8e-4dd0-aba7-2452cd453682)

Select "Next"
Enter a password
and finish the installation. When the installation is finish reboot the device.

![adpass](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/882942e0-ec6a-4d9b-a5c8-8c451b357611)

### Stage2 : Domain Setup : Create new AD users

We created users for our Domain. This allows people who has the credentials to log into a computer in the business environment.
Our naming format is as follows:

We will create a admin and user account for Ashley Williams. We will take the first name initial and attatch it to the last name followed
by a u- for user and a a- for admin account.

a-awilliams
u-awilliams

![AD add user](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/9b9a03b1-adc5-468e-9105-5c067e2fc575)

![users tab](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/dbf765e6-6e75-40f4-8f07-4c8e339e6ba3)

![user cred](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/afbe4315-2694-45e1-94bf-5acf7473afc5)

![change pass](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/b51822a6-82f8-44a0-823a-3502570e251e)

For admin accounts you must add them to the "Domain Admins" group to be able to make administrator changes and installations. Normal
users will not have the permisions to change, and install files.

![add group](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/8db76a87-98c0-4861-b71b-5987858f4c7d)

![domain admin group](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/877367b2-12f2-4196-8b97-94ef3264ef61)

![domain admin check](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/615bccab-7ca8-4966-bd19-e09434b63698)

Be sure to check names, windows is Case Sensitive.

### Stage2 : Domain Setup : Prepare Win10 to join the domain

In order to prep our windows workstation to join our DC server we must first name the windows 10 pc so we can identify it.

under control panel go to --> System --> Change Settings
under the system properties we changed the name of our workstation.

![Change PC name](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/4eec0625-fd35-4980-a821-c992a1dca695)

We then went into the our Win10 workstation and changed our IP settings. Our IP address will be obtained automatically through our DC server,
and our DNS settings are pointed to our LAN-SWITCH and our DC server.

Our last step is to sync our time with our DC. We changed the time zone to the same time as our DC, then under the Internet Time settings
we type in our full Domain Name. 

![Domain Time Sync](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/65e4911b-87aa-48b6-99cf-9cac75ef5ea4)


### Stage2 : Domain Setup : Join Win10 to the widgets.localdomain domain

To join the domain go back into the settings in which we changed the computer name to win10. Click the domain box and type in the domain
name. Afterwards you will get a prompt to login. Use an admin account that you have created.

![joining the widgets domain](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/0897f4c3-95f4-40c6-9ff1-6895a7f33f7c)

Logout of the windows10 workstation and log back in using a user, or admin account. Our station has been succesfully linked to our Domain Controller.

### Stage3 : IIS Setup : Topology

Next we will create a webserver. We will set the device up using IIS (Internet Information Service), it will be connected to our LAN-SWITCH.

![IIS Topology](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/fb8502f8-b750-45be-aef7-a3e66e87ae88)


### Stage3 : IIS Setup : Prepare a Win2012r2 server

We will rename the servers hostname to: iis
match the subnet mask with the other devices.
Give the device a static ip address.
Point the default gateway to the LAN-SWITCH.
The DNS will be pointed into the DC and LAN-SWITCH.
Synchronize the time with the other devices and join the Domain just like we did for the workstation.

### Stage3 : IIS Setup : Install the IIS role

In server manager select # Add roles and features.
Select Role Based Installation and hit next.
Select the server in which you want to install IIS on.
Select IIS Webserver to install and continues with the installation.

![iis configure setup](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/797b8a18-e2be-4da2-ba4e-e94fdbb2c881)

![server pool](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/2ca66c61-4ace-4251-825b-a58e3190d721)

![webserver iis](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/92e28911-3f6a-4c73-8513-7db63c336198)

![add features iis](https://github.com/Magee3/Building-a-Small-Business-Environment/assets/134301259/f733b46e-4f24-47af-9ab7-2a671097f3e4)

### Stage3 : IIS Setup : Setup a test webpage on IIS

We will create a test html file to see if our webserver is functioning properly.
Here is a simple HTML code we used to test the webserver:

  <html>
  <head>
  <title> IIS test validation website</title>
  </head>
  <body>
  <p>test website validation completed</p>
  </body>
  </html>
  
  Save the file as "test.html" 
  ! Make sure that the file is not a notepad file that it is actually a HTML file.
  Save the file to this location " c:\inetpub\wwwroot\test.html "
  
  You can test to see if your browser can open the file on the local iis server by typing "http://localhost/test.html" in the browser.
  
 You can test to see if other workstations in the business environment can access the file by loging into a workstation and typing
 http://iis.widgets.localdomain/test.html into the browser.

















 
 
 
 
 

 
 

