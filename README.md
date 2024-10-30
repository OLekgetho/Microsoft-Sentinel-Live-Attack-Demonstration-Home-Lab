# Microsoft Sentinel Live Attack Demonstration Home Lab

## Description 

This is a walkthrough of how I used Microsoft Azure to create a virtual machine running Windows 10. I exposed the VM to the internet and utilized Azure Log Analytics Workspace, Microsoft Defender for Cloud, and Azure Sentinel to collect and aggregate attack data, displaying it on a map in Microsoft Sentinel. I will be using PowerShell to scan the Event Viewer in the exposed VM, specifically looking for Event ID 4625, which indicates failed logon attempts, and send that data to a log file. The PowerShell script also sends the IP address of any failed logons to IPgeolocation.io via an API, allowing that information to be used in Microsoft Sentinel to map where the logon attempts originated. This project was undertaken to gain experience with SIEMs, cloud concepts and resources, APIs, and Microsoft Azure. I learned how to provision and configure resources in the cloud, how to read SIEM logs, and much more.

# Utilities & Environments Used
-  Microsoft Azure
-  Virtual Machines
-  Log Analytic Workbooks
-  Powershell
-  Microsoft Defender for Cloud
-  API's


## Program Walkthrough

The first thing I started doing is provisioning a VM as the process can be lengthy.

![Azure Portal Home Page](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(59).png)
![Azure VM](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(58).png)

In the Virtual Machine Creation process I created a new Resource Group that would hold all my future Azure resources. I decided to name the resource group "Honeypotlab" and named the virtual machine "honeypot-vm for the image I choose the "Windows 10 Pro, version 22h2 x64 Gen2". When it came to choosing the size of the VM I used "Standard_B2s", then I created the admin account. I chose a unique admin name and a 20 character password. Since I knew people would be try to log into the VM through brute-forcing and dictionary attacks a strong password is necessary.


![Azure VM](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(57).png)
![Azure VM](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(56).png)
![Azure VM](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(55).png)
![Azure VM](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(53).png)
![Azure VM](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(52).png)

After configuring the Basic section I moved to the Networking section. So now I changed the NIC network security group to "Advanced" then I clicked "Create new" link at the Configure network security group. I am going to create a new Network Security Group (NSG) and NSG is vital for controlling network traffic in virtual networks. For this project I want to allow anyone to be able to communicate with the honeypot VM. So now I created an inbound rule that will allow communication to the VM, the Source field and the Destination field and the Protocol I choose the choice to "Any". The Destination Port Ranges and Source Port Ranges I entered the asterisk (*) for anything the priority I entered "100" and I called the NSG "DANGER_ACCESS" after doing this I Deployed the VM.

![Azure VM NSG](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(50).png)
![Azure VM NSG](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(49).png)
![Azure VM NSG](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(48).png)
![Azure VM NSG](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(47).png)
![Azure VM NSG](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(46).png)
![Azure VM NSG](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(45).png)

While my Virtual Machine is deploying, I got started on setting up a Log Analytics Workspace. I added the Log Analytics Workspace in the resource group I created which was "Honeypotlab" and I named the instance "law-honeypotlab" then I created the Log Analytics Workspace.

![Azure Log ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(42).png)
![Azure Log ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(41).png)

After deploying the Log Analytics Workspace I went to the Microsoft Defender for Cloud as I have to provision enroll in some plans to be able to collect and aggregate data for Microsoft Sentinel. I also need to connect my HoneyPot-VM to Microsoft Defender so it can collect data. At this point my VM has been created so it can be connected to these services. We call these Data Connectors and I only turn on the plans for Foundational CSPM and Servers.

![Azure Microsoft Defender ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(40).png)
![Azure Microsoft Defender ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(39).png)
![Azure Microsoft Defender ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(38).png)
![Azure Microsoft Defender ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(37).png)

So now since my VM was created I could connect back into my Log Analytics Workspace and connect my VM to that service as well.

![Azure VM connect ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(36).png)
![Azure VM connect ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(35).png)

I created a Microsoft Sentinel resource and connected it to my created Log Analytics Workspace.

![Azure Sentinel ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(34).png)
![Azure Sentinel ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(33).png)

So now I had connected my Workspace with sentinel. I could connect to my VM through RDP. After I successfully logged into the VM I navigated to the Event View as it logs every event that happens in the windows system and it gives the events an EventID. For this project we are concerned specifically with EventID 4625, which is Failed Logon Attempts. These Logs can be found in the Security Tab. In the pictures below, I run another instance of Remote Desktop and try to log into the VM with the wrong password. This creates a failed logon attempt which is then logged and recorded in Event Viewer.

![VM RDP](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(32).png)
![VM RDP ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(31).png)
![VM RDP](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(30).png)
![VM RDP](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(29).png)
![VM RDP ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(28).png)
![VM RDP](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(27).png)
![VM RDP](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(26).png)

So as I know I cant ping the VM due to the Windows Defender Firewall is activated and it is blocking the ICMP Echo Requests which will make the VM undiscoverable. So to make sure that anyone on the internet can discover my VM I disabled the windows Firewall.

![Firewall Disable](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(25).png)
![Firewall Disable](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(24).png)

Now that I have disabled the firewalls I can begin to setup my powershell script. This PowerShell script will parse Event Viewer specifically looking for EventID 4625. It will then send the IP address from the failed logon attempts to the website IPgeolocation.io via an API.  The PowerShell script will then receive all that geographical data and save it as a string in a logfile named failed_rdp.log. I will use this logfile later on in the project to be able to map the attacks live in Microsoft Sentinel.

![Powershell](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(23).png)

After I open PowerShell, I paste my script that was written before the start of this project. I then save that script to the desktop as Log_Exporter. I pasted my own API into the powershell script and I ran the script. So that it was easier to read I made it so that the script outputs in pink and black the results.

![Powershell](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(22).png)
![Powershell](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(21).png)

Now that I saw that the powershell script is working as it should I headed over to Log Analytics Workbooks to create a custom log so that I can bring my failed_rdp log into Log Analytics. In Log Analytics I navigate to Settings section to "Tables" then click create and pick the option "New Custom Log (MMA-based). When creating a custom log it is going to require a sample log which it is inside the VM so to counter this I am going to open the saved logfile inside the VM and copy the contents and go back to my native computer and paste the contents in notpad and save the file to my desktop.

![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(20).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(19).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(18).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(17).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(16).png)

Next it asks for the collection path. The collection path is where the log lives in the VM, so it asks for a path that Log Analytics can take to reach that logfile. If this path is wrong, Log Analytics wouldn't be able to collect the log information. Next, we have to name our custom log. I decided to name it FAILED_RDP_WITH_GEO.

![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(15).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(14).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(13).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(12).png)

While that is being provisioned I decided to query the Event Viewer to see all the logs with an EventID of 4625 and then after waiting a while i decided to query the newly created FAILED_RDP_WITH_GEO custom log and it is indeed showing information meaning that the VM and Log Analytics as synced and is sending/receiving data. 

![Query](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(11).png)
![Query](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(10).png)

After this the next step was to setup my geomap that will show where the attacks or failed login attempts where coming from. I do this by navigating to Microsoft Sentinel and went to the Workbooks section and I added a workbook. 

![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(9).png)
![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(8).png)

So to create the map. I need to add a query that will query the data and the fields from Log Analytics and also in the query I specify that I want to exclude the data point that is called "Samplehost" since those aren't real attacks and I don't want them to populate on the map.


![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(7).png)
![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(6).png)

After I queried the data points I want, I now have to choose how I want to express/visualize them. In this case I want them visualized as a map! I choose the map setting and then configure the map to plot the attacks by latitude/longitude. I apply the settings and my map is done.

![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(5).png)
![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(4).png)
![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(3).png)

After a few Minutes and right before I decided to stop the project, you can see that the is more than 1000 failed logon's and mostly they came from Ukraine and the is 2 from Canada and 3 from South Africa. They were using automated brute-forcing software to try thousands of different password combinations and usernames. This is why it was important to use strong passwords and uncommon usernames!
![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(1).png)
