# Microsoft Sentinel Live Attack Demonstration Home Lab 

## Description 

This is a walkthrough of how I used Microsoft Azure to create a virtual machine running Windows 10. I exposed the VM to the internet and utilized Azure Log Analytics Workspace, Microsoft Defender for Cloud, and Azure Sentinel to collect and aggregate attack data, displaying it on a map in Microsoft Sentinel. I will be using PowerShell to scan the Event Viewer in the exposed VM, specifically looking for Event ID 4625, which indicates failed logon attempts, and send that data to a log file. The PowerShell script also sends the IP address of any failed logons to IPgeolocation.io via an API, allowing that information to be used in Microsoft Sentinel to map where the logon attempts originated and then I made an alert that informs me of IP's that failed to login more than 3 times. This project was undertaken to gain experience with SIEMs, cloud concepts and resources, APIs, and Microsoft Azure. I learned how to provision and configure resources in the cloud, how to read SIEM logs, and much more.

# Utilities & Environments Used
-  Microsoft Azure
-  Virtual Machines
-  Log Analytic Workbooks
-  Powershell
-  Microsoft Defender for Cloud
-  API's


## Program Walkthrough

The first thing I did was provision a VM, as the process can be lengthy.

![Azure Portal Home Page](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(59).png)
![Azure VM](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(58).png)

In the virtual machine creation process, I created a new resource group to hold all my future Azure resources. I named the resource group "Honeypotlab" and designated the virtual machine as "honeypot-vm." For the image, I selected "Windows 10 Pro, version 22H2 x64 Gen2." When it came to choosing the size of the VM, I opted for "Standard_B2s" and then created the admin account. I chose a unique admin name and a 20-character password. Since I anticipated that people would attempt to log into the VM using brute-force and dictionary attacks, a strong password was essential.

![Azure VM](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(57).png)
![Azure VM](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(56).png)
![Azure VM](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(55).png)
![Azure VM](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(53).png)
![Azure VM](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(52).png)

After configuring the Basic section, I moved to the Networking section. I changed the NIC network security group to "Advanced" and clicked the "Create new" link in the Configure network security group section. I created a new Network Security Group (NSG), which is vital for controlling network traffic in virtual networks. For this project, I wanted to allow anyone to communicate with the honeypot VM. I created an inbound rule to enable communication to the VM, setting both the Source and Destination fields to "Any," and selecting "Any" for the Protocol as well. For the Destination Port Ranges and Source Port Ranges, I entered an asterisk (*) to allow all traffic. I set the priority to "100" and named the NSG "DANGER_ACCESS." After completing these steps, I deployed the VM.

![Azure VM NSG](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(50).png)
![Azure VM NSG](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(49).png)
![Azure VM NSG](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(48).png)
![Azure VM NSG](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(47).png)
![Azure VM NSG](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(46).png)
![Azure VM NSG](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(45).png)

While my virtual machine was deploying, I began setting up a Log Analytics Workspace. I added the Log Analytics Workspace to the resource group I created, which is named "Honeypotlab," and I called the instance "law-honeypotlab." After that, I created the Log Analytics Workspace.

![Azure Log ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(42).png)
![Azure Log ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(41).png)

After deploying the Log Analytics Workspace, I went to Microsoft Defender for Cloud, where I needed to enroll in some plans to collect and aggregate data for Microsoft Sentinel. I also needed to connect my Honeypot-VM to Microsoft Defender so it could gather data. At this point, my VM had been created and was ready to connect to these services. We refer to these as Data Connectors, and I only enabled the plans for Foundational CSPM and Servers.

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

I successfully connected my Workspace with Sentinel and accessed my VM through RDP. After logging into the VM, I navigated to the Event Viewer, which logs every event that occurs in the Windows system and assigns each event an Event ID. For this project, we are specifically concerned with Event ID 4625, which indicates failed logon attempts. These logs can be found in the Security tab. In the images below, I ran another instance of Remote Desktop and attempted to log into the VM using the wrong password. This created a failed logon attempt, which was then logged and recorded in the Event Viewer.

![VM RDP](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(32).png)
![VM RDP ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(31).png)
![VM RDP](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(30).png)
![VM RDP](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(29).png)
![VM RDP ](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(28).png)
![VM RDP](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(27).png)
![VM RDP](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(26).png)

I knew I couldn't ping the VM because the Windows Defender Firewall was activated, blocking ICMP Echo Requests and making the VM undiscoverable. To ensure that anyone on the internet could discover my VM, I disabled the Windows Firewall.

![Firewall Disable](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(25).png)
![Firewall Disable](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(24).png)

Now that I have disabled the firewalls, I can begin setting up my PowerShell script. This script will parse the Event Viewer, specifically looking for Event ID 4625. It will then send the IP addresses from the failed logon attempts to the website IPgeolocation.io via an API. The PowerShell script will receive all the geographical data and save it as a string in a log file named failed_rdp.log. I will use this log file later in the project to map the attacks live in Microsoft Sentinel.

![Powershell](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(23).png)

After opening PowerShell, I pasted the script I had written before starting this project and saved it to the desktop as Log_Exporter. I inserted my own API into the PowerShell script and then ran it. To make the output easier to read, I formatted the results to display in pink and black.

![Powershell](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(22).png)
![Powershell](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(21).png)

Now that I confirmed the PowerShell script was working correctly, I headed over to Log Analytics Workbooks to create a custom log so I could import my failed_rdp.log into Log Analytics. In Log Analytics, I navigated to the Settings section and selected "Tables," then clicked "Create" and chose the option "New Custom Log (MMA-based)." Creating a custom log requires a sample log, which is located inside the VM. To address this, I opened the saved log file within the VM, copied its contents, and then returned to my local computer to paste the contents into Notepad, saving the file to my desktop.

![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(20).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(19).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(18).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(17).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(16).png)

Next, I was prompted for the collection path. The collection path specifies where the log resides in the VM, so I needed to provide a path that Log Analytics could use to access the log file. If this path is incorrect, Log Analytics wouldn’t be able to collect the log information. After that, I named my custom log, deciding on the title FAILED_RDP_WITH_GEO.

![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(15).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(14).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(13).png)
![Custom Log](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(12).png)

While that was being provisioned, I decided to query the Event Viewer to view all logs with an Event ID of 4625. After waiting a while, I queried the newly created FAILED_RDP_WITH_GEO custom log, and it was indeed showing information. This indicates that the VM and Log Analytics are synced and successfully sending and receiving data.

![Query](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(11).png)
![Query](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(10).png)

After this, the next step was to set up my geomap to show where the attacks or failed login attempts were coming from. I did this by navigating to Microsoft Sentinel, going to the Workbooks section, and adding a new workbook. 

![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(9).png)
![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(8).png)

To create the map, I needed to add a query that would retrieve data and fields from Log Analytics. In the query, I specified that I wanted to exclude the data point labeled "Samplehost," as these entries do not represent real attacks and I didn’t want them to appear on the map.


![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(7).png)
![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(6).png)

After I queried the data points I want, I now have to choose how I want to express/visualize them. In this case I want them visualized as a map! I choose the map setting and then configure the map to plot the attacks by latitude/longitude. I apply the settings and my map is done.

![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(5).png)
![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(4).png)
![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(3).png)

After a few minutes, just before I decided to wrap up the project, I observed that there were more than 1,000 failed logins, with most attempts originating from Ukraine, along with 2 from Canada and 3 from South Africa. It was evident that automated brute-forcing software was being used to try thousands of different password combinations and usernames. This underscores the importance of using strong passwords and uncommon usernames!

![Microsoft Sentinel](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(1).png)

Now I created a alert that informs me when which IP has failed to login in more than 3 times. So I navigated to Microsoft Sentinel under the configuration at the Analtics section. The was already a alert created with a serverity of High. So I clicked the button create and choose the "Scheduled query rule", I configured the rule and called it "Multiple Login attempts from the same IP" and the severity is "Low" as I intentionally exposed the VM on the internet so the will be alot of people trying to gain access on it. If it was a vm that can only be accessed by an organization then the severity would be medium if indiviual in the workplace made more than 3 mistakes and high if it is an indiviual outside the organization.


![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(70).png)
![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(69).png)
![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(68).png)

After configuring the General section i was on the Set rule logic section and I had to create a rule query basically the logic for my analytic rule. So my rule is that the logs that have an EventID of 4625 count them by the IPaddress and if the counted IPaddress is counted more than 3 times it will alert me and which IP it is. Then after I clicked next till the review + create section and clicked save to save the rule. Minutes after I saved the rule the was already an IP that was captured the IP orginates from Netherlands at Amsterdam. I wanted to see if it was the only IP that i can find so I when to logs and I queryed them and I saw the only IP appearing is the one captured and it has 310 failed attempts.

![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(67).png)
![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(66).png)
![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(65).png)
![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(64).png)
![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(63).png)
![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(62).png)
![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(61).png)
![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(60).png)
![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(54).png)
![Microsoft Sentinel Alert](https://github.com/ofentse579/Images/blob/main/Microsoft%20SIEM/SIEM%20(51).png)
