AIM: Creating a Virtual Lab
In order to enhance my practical skills in system administration and cybersecurity, I set up a virtual lab environment using VirtualBox. The process was carried out in a systematic step by step, as follows:
Step 1: Installing Virtual Machines
Using Oracle VirtualBox as the virtualization platform, I created and configured three distinct virtual machines, each serving a specific purpose in the lab:
1.	Ubuntu Server
2.	Windows 11
3.	Parrot OS
Step 2:
As part of building my virtual cybersecurity lab, I installed Splunk on the Ubuntu Server VM to serve as the central SIEM (Security Information and Event Management) system. Splunk enables real-time collection, indexing, and analysis of machine data on the network.
Command: 
1.	wget -O splunk-9.4.1-e3bdab203ac8-linux-amd64.deb https://download.splunk.com/products/splunk/releases/9.4.1/linux/splunk-9.4.1-e3bdab203ac8-linux-amd64.deb
2.	Go to terminal and paste the above command
3.	Use dpkg -I Splunk-version.deb: to install the Splunk Debian package on the system
4.	 /opt/splunk/bin/splunk is the directory where the splunk is stored
5.	Now we want to boot and start the splunk : for that we have to use this command
6.	Sudo /opt/splunk/bin/splunk enable boot-start
7.	Accept the licence and set the username and password for the splunk
8.	We have to open the firewall :
9.	Sudo ufw allow OpenSSH
10.	Sudo ufw allow 8000
11.	Sudo ufw status : to check the status of the firewall
12.	If inactive
13.	Sudo ufw enable
14.	To start the spunk server
15.	Sudo /opt/splunk/bin/splunk start
16.	Ip a 
17.	Visit http://ubuntuserver:8000

 

Step 3: Installing Zeek & Suricata for Intrusion Detection and Network Monitoring
To enhance the detection capabilities of my virtual lab, I installed two powerful open-source tools Zeek and Suricata on the Ubuntu Server VM. Both tools are used to monitor and analyze network traffic for suspicious behavior and potential security threats.
Zeek .
Suricata
With Suricata and Zeek operating, my lab is able to gather high-quality network metadata (via Zeek) and perform real-time signature-based analysis (via Suricata). Both of these applications significantly boost the lab's capability for detecting, analyzing, and responding to simulated cyber threats in a controlled and experimental setting.
Installing guide for zeek
1.	Sudo apt update && sudo apt install zeek -y
2.	docker run --rm -it zeek/zeek:latest zeek –version
3.	docker run --rm -it zeek/zeek:latest /bin/bash
4.	apt-get update
5.	apt-get install -y --no-install-recommends g++ cmake make libpcap-dev
6.	zeek --version
Installing guide for suricata
1.	Sudo apt install suricata -y
2.	Sudo nano  /etc/suricata/suricata.yaml : edit the interface : eth1
3.	Sudo systemctl enable –now suricata: restart suricata
Forward IDS logs to splunk
1.	Sudo apt install rsyslog -y
2.	Sudo nano /etc/rsyslog.conf
3.	*.* @@splunk-IP:514 :: add this line in the file
4.	Sudo systemctl restart rsyslog
Step 4: 
1.	installing Sysmon for windows logging
2.	setting up configuration file
3.	downloading the sysmonconfig.xml from github
4.	Sysmon -accepteula -I sysmonconfig.xml
5.	Verifiny the installation 
6.	Sc query Sysmon

Step 5
1.	Installing the splunk universal forwarder on windown 11 vm
2.	Setup the deployment server
3.	@@ip 8089
4.	Open powershell 
5.	Cd “C:\Program files\splunk\Universalforwarder\bin”
6.	.\splunk add forward-server @@ IP:9997
7.	.\splunk enable boot-start
8.	.\splunk restart

Configure Splunk to receive logs on ubuntu
1.	Sudo /opt/splunk/bin/splunk enable listen 9997
2.	Sudo /opt/splunk/bin/splunk restart
3.	Open firewall for port 9997
4.	Sudo ufw alllwo 9997/tcp
5.	Sudo ufw reload
6.	Verify the port litening
7.	Netstat -tulnp | grep 9997
Configure splunk inputs for Sysmon logs
1.	Sudo nano /opt/splunk/etc/system/local/input.conf
Write this commad: 
[WinEventLog://Microsoft-Windows-Sysmon/Opreationl]
Disabled = 0
Index = Sysmon
Sourcetype = WinEventLog:Sysmon
2.	Restat splunk
3.	Sudo /opt/splunk/bin/splunk restart
Verify Sysmon log in splunk
1.	Open splunk
2.	Go to search and reporting 
3.	Search the following
4.	Index=Sysmon sourcetype=”WinEventLog:Sysmon”
5.	To verify from windows
6.	Use this command
7.	telnet @@Splunk-Ip 9997
Configuring splunk to receive logs from windows:
first verifying from ubuntu;
 

 
Verified now setting up for windows.
Step 1:
 
Step 2: 
Step 3: 
Step 4:
 


Edit file “winlogevent.yaml” 
To configure the winlogevent.yaml file for sending Windows logs to a Splunk HEC endpoint, the output.http section must be defined. This involves configuring the target server address, authentication token, and SSL verification options to be used within the lab environment.
----------------------------------------------------------------------------------------------------------------output.http:
  hosts: [http://@@splunk-Ip]
  bearer_token: 0adec56b—Splunk-bearer-token
  ssl.verification_mode: none
----------------------------------------------------------------------------------------------------------------simulating and testing to test the Splunk from windows.

 
Checking Splunk
 

Logs Received. 😊
Next challenges:
1.	Simulating Cyber Attacks and Detection
2.	Integration of the ELK Stack
