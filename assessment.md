## Ticket #1
#### There are multiple reports of employees located in the USERNet subnet who cannot get to `www.domain.edu`, and they are being redirected to a suspicious site. A help desk technician states that the server team recently installed updates to DMZ_Server_3 which acts as the DNS Server for the organization.  
  - After discovering that the workstations and DNS server were displaying the wrong webpage, a detailed investigation was carried out to identify the source of the issue.
Firstly, it was noted that the DNS server was responsible for listing the wrong IP address of the website, which was the same IP address as another machine on the same subnet. This was identified as a conflict that could lead to several problems, and hence, the DNS settings were examined to identify the cause of the issue.
  - To rectify this issue, the official, public IP address of the website was found by using nslookup. Several IP addresses were identified, and it was decided to work with the primary public IP of the website, which was 151.101.2.224. This IP address was then updated in the DNS server from the incorrect IP of 10.10.20.2. This ensured that the Windows workstations were able to access the correct website.
  - However, the issue persisted with the two Linux workstations. To resolve this, the nameserver was updated in the /etc/resolv.conf file to the IP address of DMZ server 3 (10.10.20.3). This allowed the Linux workstations to access the website successfully.
It is essential to ensure that the IP addresses used in DNS servers are correct to avoid conflicts that could cause issues with accessing websites or other online resources. 

<br>
<div align="center">
  <p>Use nslookup to find the correct address</p>
  <img src="/screenshots/Ticket1cmd.png" alt="ip" width="600">
</div>
<br><br>
<div align="center">
  <p>Add the correct IP address to the DNS Manager</p>
  <img src="/screenshots/Ticket1dns.png" alt="dns" width="600">
</div>
<br><br>
<div align="center">
  <p>Configure the nameserver to point to the IP address of the DNS server</p>
  <img src="/screenshots/Ticket1resolv.png" alt="resolv.conf" width="600">
</div>
<br><br>
<div align="center">
  <p>Traceroute to the correct IP address works and the website loads
</p>
  <img src="/screenshots/Ticket1traceroute.png" alt="traceroute" width="600">
</div>
<br>
<br>

## Ticket #2
#### A complaint came in that a certain organization is hosting an illegal FTP site to download copyrighted software. The security team has provided a pcap file capturing all FTP traffic on the network. They've asked you to identify where the FTP site is being hosted.
  - Firstly, the Pcap file was opened on the machine, and the default application used to open the file was Wireshark. The Wireshark interface allowed me to filter the results by FTP, as shown in the screenshot.
  - Upon filtering the results, the first IP that had FTP ports open was identified as the WAREZ FTP server. This was determined from the name displayed in the interface, which was "WAREZ FTP". Further investigation in the interface revealed that the IP address of the rogue FTP server was listed as 10.10.20.2.
This detailed approach of using Wireshark to filter results by FTP ports allowed for the identification of the rogue FTP server on the network.
<br>
<div align="center">
  <p>Wireshark shows the destination of the FTP protocol
</p>
  <img src="/screenshots/Ticket2wireshark.png" alt="wireshark" width="600">
</div>
<br>
<br>

## Ticket #3
#### The host "Ubuntu_Server" can’t get to any of the assigned networks or the internet, which is preventing the server from pulling the required security patches.

  - I tested the Ubuntu Server’s internet connection by trying to run sudo apt update and running a ping command to 8.8.8.8. The goal was to test for any connectivity to the internet or another device on the network. Ping to the internet, so I decided to use a traceroute command to see where in the network there was a connectivity issue. Traceroute showed that routers were sending data to each other in a loop, from 10.10.80.1 and 10.10.80.254. This means that data was connecting to router 4, and then it was being sent back to its next destination, which was the previous step in the loop. I went into router 4 to see which settings were connected with which interfaces. I entered entered show ip route to show the route table of the device to make sure all devices were properly listed. I decided to enter the configuration of Router 4, using ‘configure’ to see if the next-hop was properly configured. It was not, and that was the reason for the traceroute loop. There needed to be a next hop to the internet via 10.10.70.254 and also a next hop to the Ubuntu server via 10.10.80.1
  - There was only one static route, and it was pointed to 10.10.80.1 as the next hop. I changed the internet facing route with: set protocols static route 0.0.0.0/24 next-hop 10.10.70.254. I then added a static route facing the Ubuntu server with: set protocols static route 10.10.90.0/24 next-hop 10.10.80.1 so that data could be sent to the server. The steps were to use: configure, <command>, commit, save, exit. I then tested connectivity to the internet from the Ubuntu server, using ping and traceroute commands to 8.8.8.8. The commands worked successfully, and I was able to run sudo apt update to start updating the Ubuntu Server with internet connectivity.
<br><br>
<div align="center">
  <p>Traceroute to the destination address fails
</p>
  <img src="/screenshots/Ticket3ping.png" alt="traceroute" width="600">
</div>
<br><br>
<div align="center">
  <p>The router is missing a static route on the return path to receive the ping
</p>
  <img src="/screenshots/Ticket3route.png" alt="traceroute" width="600">
</div>
<br><br>
<div align="center">
  <p>Add the missing static route
</p>
  <img src="/screenshots/Ticket3staticroute.png" alt="traceroute" width="600">
</div>
<br><br>
<div align="center">
  <p>Ping and traceroute to the destination address now work
</p>
  <img src="/screenshots/Ticket3addedroute.png" alt="traceroute" width="600">
</div>
<br><br>
<div align="center">
  <p>Traceroute to the correct IP address works and the website loads
</p>
  <img src="/screenshots/Ticket3success.png" alt="traceroute" width="600">
</div>
<br>
<br>

## Ticket #4
#### A user complains that he cannot access the internet or network resources on his company laptop (Windows_Laptop_1) when it is connected via an ethernet cable to the office network.

  - This workstation had no connection to the internet, so I decided to check IP configuration with the command ipconfig. It was found that the machine was assigned an Automatic Private IP Address (APIPA) which indicated that there was no DHCP server available to assign an IP address. To resolve this, I manually assigned a static IP address of 10.10.40.4, in accordance with the network diagram on GNS3.
  - In addition, I updated the default gateway to the DNS of DMZ Server 3, which provided the necessary routing to connect to the internet. This was done to ensure that the workstation was able to communicate with other devices on the network and access the internet.
To confirm that the issue was resolved, I performed a ping test and successfully loaded websites on the workstation. This ensured that the workstation was connected to the network and had internet connectivity.
<br>
<div align="center">
  <p>Ipconfig shows an APIPA assigned address
</p>
  <img src="/screenshots/Ticket4apipa.png" alt="apipa" width="600">
</div>
<br><br>
<div align="center">
  <p>Add a static IP on the same subnet to the laptop
</p>
  <img src="/screenshots/Ticket4ip.png" alt="staticip" width="400">
</div>
<br>
<br>

## Ticket #5
#### A coworker states that she worked on a ticket to allow access through the firewall to DMZ_Server_1. There is now no access to the server from any device outside its network.

  - Since there was limited connectivity to DMZ Server 1 (10.10.20.1), I decided to test which servers were able to connect. I used the ping command, and was able to obtain a successful response from the device. I tried to ping 10.10.20.1 from the workstations, and was unsuccessful. Since there was a previous ticket regarding connectivity through the firewall, I decided to investigate the firewall.

  - I opened the OPNSense Firewall to check settings using the URL given (10.10.60.254) on a device on the same subnet, DMZ Server 3.  I used the address 10.10.60.254 in the browser, and was able to access the rules list in Firewall > Rules > DMZ. There was a rule that explicitly denied all traffic to DMZ Server 1. Someone must have placed this rule here, and this is the reason why devices could not access the device through the firewall!
To resolve the issue, I removed the rule that was restricting access, as shown in the screenshot.  This restored access to 10.10.20.1, and I tested connectivity by using  the ping command from an external workstation, Linux 1. The ping was  successful, indicating that the issue had been resolved.
<br>
<div align="center">
  <p>Disable the firewall rule that is blocking the connection
</p>
  <img src="/screenshots/Ticket5firewall.png" alt="firewall" width="600">
</div>
<br><br>
<div align="center">
  <p>Ping to the DMZ Server is now successful.
</p>
  <img src="/screenshots/Ticket5ping.png" alt="pingip" width="600">
</div>
<br>
<br>
 
## Ticket #6
#### Your local cyber security team is requesting to know what ports are open on DMZ_Server2 to identify services that may be running outside of the permitted services. Permitted services are 22/ssh, 135/msrpc, 3389/ms-wbt-server, and 8080/http-proxy.

  - I needed to check which ports were open on another device on the same subnet, so I decided to use Zenmap. I installed Zenmap on DMZ Server 3 and used it to run an nmap scan of DMZ Server 2 (10.10.20.2) with a quick scan to see which ports were open. It reported the following ports as open, 21, 22, 23, 80, 135, 139, 443, 3389, 8080, and 9999. Permitted services are 22/ssh, 135/msrpc, 3389/ms-wbt-server, and 8080/http-proxy, so we would need to block the other open ports for added security. Zenmap was a great tool where I could easily compare the ports that were open and closed.
<br>
<div align="center">
  <p>Zenmap displays the open ports
</p>
  <img src="/screenshots/Ticket6zenmap.png" alt="zenmap" width="450">
</div>
<br>
<br>

