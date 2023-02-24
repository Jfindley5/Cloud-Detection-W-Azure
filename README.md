<h1>Cloud Detection with Azure</h1>

<h2>Description</h2>
In this blog, we're going to walk through the process how to build a cloud detection lab in Azure.
<br />

<h2>Environments Used </h2>
<p>Microsoft Azure <br>
Microsoft Remote Desktop</p>

<h2>In this project, we will</h2>
<p> - Understand Windows Security Event logs</p>
<p> - Configure Windows Security Policies</p>
<p> - Use MITRE ATT&CK to map adversary tactics, techniques, detection and mitigation procedures</p>
<p> - Configure and Deploy Azure Resources: Virtual Machines, Azure Sentinel, and Log Analytics Workspace</p>
<p> - Use Data Connectors to bring data into Sentinel for Analysis</p>
<p> - Use KQL to query Logs</p>
<p> - Implement Network and Virtual Machine Security Best Practices</p>
<p> - Write Custom Analytic Rules to detect Microsoft Security Events</p>

<br>
<p>Detection: In cybersecurity, detection is the process of analyzing an environment for any unusual behavior that might lead to a network being compromised.</p><br>

<p>The tool that allows us to do this is a SIEM(security information event management), a tool that allow security professionals to have a centralized location to be able to analyze data from a variety of sources such firewalls, intrusion prevention systems, etc., all of these things generate logs that we will connect to the SIEM. From there we can write alerts and they will monitor for certain occurrence that could be indicators of malicious activity. We can then investigate the alert and decide if its really an alert or if its a false positive which will help us with the next steps.</p>



<h3>Part 1 (Can skip if you already have an azure account)</h3>
<b>Create a free Azure account</b>
<p> - Go to https://azure.microsoft.com/en-us/free/ to sign up for a free azure account.</p>


<b>Create a Resource Group</b>
<p> - Resource Group is a logical containers for all of our resources. Windows 10 Virtual Machine, Azure Sentinel Resources, and Log Analytics Workspace will be the resources.<br>
- Go to the Azure Portal Search Bar and type in "Resource Group" to create your resource group.</p>

<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*FJpeNrGjvPBIoL2hQYuyXA.png" height="80%" width="80%" alt=""/>
 <br>Click "Review + create" after you fill in the appropriate boxes. </p>
<p align="center"> 
 <br>
<img src="https://cdn-images-1.medium.com/max/1600/1*2y8Aoye7WTo3dAB6nrwCPQ.png" height="80%" width="80%" alt=""/>
 <br>Click "Create" & now you have your first Resource Group. </p>
<br>
<p>Next, we will create our <b>Virtual Machine</b> and link it to our resource group. <br>
- Go to the Azure Portal Search Bar and type in "Virtual Machine" and tap "Create" and fill out the appropriate boxes.</p>
<br>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*nHk_dqrYB5XdBHkxya-qoA.png" height="80%" width="80%" alt=""/>
 </p>
 <br>
 <p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*AzMUkv_L88fikIjhbVMaWQ.png" height="80%" width="80%" alt=""/>
 <br>#Remember your Username & Password. You will need this to authenticate your Virtual Machine. Click "Review & Create". </p>
 <br>
 <p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*o275xq2pRLBZTvc9-1iirA.png" height="80%" width="80%" alt=""/>
 <br>We will use the default settings for Disk, Networking, Management, Advanced, & Tags. Click "Create". </p>
 <br>
 <p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*V3koWnagQjRIUiE5cU1mzw.png" height="80%" width="80%" alt=""/>
 </p>
 <br>
<p>
 - After creating a virtual machine in Azure, it will be placed on a virtual network and will be assigned an IP address. Network Security Group is the default security feature implemented in Azure. Network Security Group is used to filter traffic (based on rules) to and from Azure resources. <br>
- Go back to the resource group we created and you'll be able to see the Virtual Network and Network Security Group. Mine is "cloudemo-vnet" and "cloudemo-nsg". <br>
- Select "NSG" to see the default rules.
</p>
<br>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*TN2U8K32khz7Qujn7Lymng.png" height="80%" width="80%" alt=""/>
 <br>List of rule that we allow access to and from the vm.
 </p>
 <br>
<p>
- When we deployed the VM we enabled RDP. <br>
- If you look at the RDP rule you can see that it allows any connection and is definitely a security risk.
</p>
<br>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*O_KPZelYyYzg01qWbGQIuA.png" height="80%" width="80%" alt=""/>
 <br>List of rule that we allow access to and from the vm.
 </p>
 <br>
<p> - RDP is needed to access the vm but with the default setting it's going to allow anyone who obtains the public IP address a connection to the vm. With this default security feature, it makes the vm vulnerable to password spray & brute force attacks. <br>
- We should leave the port open but limit access to only when its needed and for authorized users. In order to achieve this we have to enable "Just In Time Access". <br>
- Just In Time Access - only allows access bases on least privilege and time-based restrictions. It gives you the option to restrict certain IP addresses and RBAC roles. Since you created the azure account, you are a Global Admin, so you will be able to access the account.
</p><br>
<p><b>Microsoft Defender</b><br>
- Microsoft Defender - an Azure service that allows you to check your overall security in Azure and to enhance your security. <br>
- In the search bar type "Microsoft Defender" and select services. Next, select "environment settings" and select your subscription. By default, all of the security services are off. Select "Enable all" and select save. 
</p><br>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*cQApmkwAzNRCgpCx0D36Fw.jpeg" height="80%" width="80%" alt=""/>
 <br>Select the Enable All Microsoft Defender for Cloud Plans. You will be given a 30 free trial so be sure to disable when finished with the lab to avoid any cost.
</p><br>
<p> - Next, Select "Workload Protections" on the left side bar. On the bottom of the page in the Advance Protection section, select "Just in time vm access" and select "Enable JIT on vm" on the vm the we previously created. This will allow us to connect to the vm. <br>
- Lets go back to our Virtual Machine and select "connect" on the side bar, select "My IP" and select "Request Access". Select the " Networking" tab.
 </p><br>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*TG_pB7R4h-c2yYtjCJKQTw.jpeg" height="80%" width="80%" alt=""/>
 <br>The rules are different from before. RDP traffic is allowed for certain amount of time.
</p><br>

<p> - "JITRULE" will execute first because of rule priority and it's above the "RDP" so it will execute first and deny the request instead of letting everything connect to the VM.</p><br>

 Create <b>Log Analytic Workspace</b> and <b>Deploy Microsoft Sentinel</b><br>
 
 
<p>- In the search bar type "Log Analytic Workspace" and select "Create Log Analytic Workspace". Next, select "environment settings" and select your subscription. We will you the same resource group from before. Select "Review + Create" & "Create". 
- Next, in the search bar type "Sentinel" and select "Create Microsoft Sentinel". Select the workspace we just created to connect to and click "Add". Now we should see that it was deployed. </p><br>

 <p>Connecting the Data to <b>Microsoft Sentinel</b></p>
 <p>- <b>Microsoft Sentinel</b> - is our Azure version of a SIEM.</p>
<p>- We will use "Data connectors" to create a collection rule to be able to bring data from our VM and analyze it. Select "Data connectors" on the sidebar at the bottom and it gives us the option to select the type of data that we want to collect to our SIEM. Search "Windows" and select "Windows Security Events via AMA" and "Open Connector Page" on the bottom left. <br><br>
- Select "+Create data collection rule", name it, and connected it you our resource group that we created. Go to the Resource tab and select "+Add Resource(s)" and we will select the VM that we created at the beginning.
 </p><br>

<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*HMZhq8v3_rpXFFJYh8Wymg.png" height="80%" width="80%" alt=""/>
 <br>Now your vm should be showing.
</p><br>
<p>- Go to the Collect tab and select "All Security Events", "Review + create" and "Create".</p>

<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*Tdu0l3UkF4lcf2DZMqNGBw.png" height="80%" width="80%" alt=""/>
</p><br>
<p>Generate Security Events <br>
- Next, we will do some changes in windows that will generate security alerts. Go to your VM and select "start" at the top of the page to start the vm. Copy your public Ip address and use either Remote Desktop connection(Windows) or Microsoft Remote Desktop(macOS) or any other RDP on your pc to access our VM. <br>
- Paste the public Ip in for the computer section and use your username and password you used when you created your vm. <br>
- Once you're in, search and open "Event Viewer"</p>

<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*fC2gz3MPFg2peWuauK1zDQ.png" height="80%" width="80%" alt=""/>
 <br>Select Windows Logs
</p><br>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*_IXMR0eHfIPJOkmAwdFSxQ.png" height="80%" width="80%" alt=""/>
 <br>
</p><br>

<p>- On the sidebar, you can see the types of logs that Windows collects. Select "Security" and its looks like there are a lot of security events. Click "find" on the right sidebar and type "4624" to look more in-depth at one of the events. After selecting 4624, we can see that it was an unsuccessful login attempt and, if needed, we could also view more details that could be helpful for potential investigation.</p><br>

<b>Kusto Query Language</b><br>
<p>- Every SIEM has a search language that is used to extract data from Logs. Microsoft Sentinel search language is KQL. We will use basic KQL commands to extract data. <br>
- Navigate back to Microsoft Sentinel and you should be able to see that we have events now. Select Logs on the sidebar, and we will enter the following commands where it says "Type your query". Click Run and you will see all the login attempts.</p>

<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*zs6lqr8_frV6UgeLwedCNg.png" height="80%" width="80%" alt=""/>
 <br> Security Event refers to the event table we are pulling the data from. All the events we observed in the event viewer are stored there. Where command filters on a specific category. In this case, we only want events that correspond to successful logins. Project command will specify what data to display when the query is run so, in this specific scenario, we want to only see the time the logon event occurred, what computer it came from and what account on this computer-generated the event.
</p><br>

<p>- If we go back to our Sentence dashboard we could see that we have a bunch of events but not alerts or incidents. The main function of the SEIM is to be alerted of security incidents or potential risks going on in our environment in real-time. <br>
- It's important to know the difference between these, Security event is any observable occurrence in our environment. A successful log-on is a security event and we don't want to be alerted every time someone successfully login. We want to be alerted for a thing that could be a sign of compromise or unusual activity in the environment and that's where security incidents come in. Usually, do an investigation of if it's something that needs to be looked into or if it's just a false positive.<br>
- Analytic Rule - will check our VM for an activity that matches the rule and generate alerts anytime that activity is observed. Details will be provided in the alert to help analyst start their investigation. <br>
- On the sidebar go down to configuration and select "Analytic"
 </p>

<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*hk4Q3DjjXRIArpPmLt8Evw.png" height="80%" width="80%" alt=""/>
 <br>Gives us the option to create our own or select one of these that Microsoft already has.
</p><br>

<h4>Schedule Task and Persistence Techniques</h4>
<p>- We will create a rule to detect malicious activity on your VM. We will create a scheduled task in theWindows Task scheduler and we will be able to automate certain activities on our machine. For example, we could schedule a task that opens Internet Explorer every day at a certain time. <br>
- Let's visit https://attack.mitre.org/techniques/T1053/ website and see what they say about scheduling tasks. The MITRE ATT&CK is a collection of techniques that attackers will you to compromise your environment. <br>
- Click on "Scheduled Task" and what they say about it is "Adversaries may abuse task scheduling functionality to facilitate initial or recurring execution of malicious code. Utilities exist within all major operating systems to schedule programs or scripts to be executed at a specified date and time". <br>
- Attackers want to maintain persistence on your system and in order for persistence to be maintained, the attacker will associate the malware with a scheduled task. For example, the attacker can set the scheduled task for the malware to run anytime the user logon. As analysts, we should be aware of scheduled tasks. <br>
- We now set up a scheduled task, not for it to be malicious but just to see how it would come to the SIEM. The default windows will not log the schedule tags if we created one, so we have to change the security policy. Search and open "Local Security Policy" in the Windows search bar, click the drop-down arrow of "Advanced Audit Policy Configuration", click the drop-down arrow of "System Audit Policies", select "Object Access", and select "Audit Other Object Access". Then Enable "Success" and "Failure". <br>
</p>

<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*QIB0L12gesRR6mgQM_Iaww.png" height="80%" width="80%" alt=""/>
 <br>Logging is enabled for the scheduled task event.
</p><br>

<p>Create Scheduled Task</p>
<p>- Search and open "Task Scheduler" in windows search bar and click "Create task". Add a name and change "Configure for" to windows 10.</p>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*wEHh6Mf0tlzLfhZ8OtZy3g.png" height="80%" width="80%" alt=""/>
 <br>
</p><br>
<p>- Select the triggers tab and click "new" and choose a time thats close to the current time that you're doing this project.</p>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*xvu-16pD3KrnTUoiEqlS0A.png" height="80%" width="80%" alt=""/>
 <br>
</p><br>
<p>- Select the actions tab to select a program to start. I selected Microsoft Edge to run everytime this task run.</p>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*EElzmUE0D6w-Sl6sd3yehA.png" height="80%" width="80%" alt=""/>
 <br> Skip conditions and settings and click "OK".
</p><br>
<p>
Now we will write our own Analytic Rule<br>
- Go to Microsoft Sentinel homepage and select "Analytic Rules",click "Create" at the top, and click "Scheduled query".</p>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*22EzPlPG_TcLx3WbQzddQQ.png" height="80%" width="80%" alt=""/>
 <br>
</p><br>
<p>- Click "Set rule logic" and here we will come up with and alert logic (KQL query) that will cause our alert to explode.</p>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*lZJksIGi4maAw0wt8ZKAYw.png" height="80%" width="80%" alt=""/>
 <br>
</p><br>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*IIzIkliAbuJj7-kAfppDLw.png" height="80%" width="80%" alt=""/>
 <br>
</p><br>
<p>- After clicking on the drop-down arrow and expand one of the events, you will see a lot of useful data but not in the best format. We also have to go into the event to get additional information but we would rather it populate in the columns which would make it easier for analysts.</p>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*v-sXuyYafLAuonF7acro3g.png" height="80%" width="80%" alt=""/>
 <br>
</p><br>
<p>- The Parse command allows us to be able to extract data from the Event Data Field that we find important, instead of looking the all of the details and finding the information we need. The Parse command will reduce a lot of time. <br>
- Now the computer will automatically display the SubjectUserName, TaskName, etc.</p>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*gJF4Z95pAsy6SEMhAGGkeA.png" height="80%" width="80%" alt=""/>
 <br> Alert Enriching section allows the entity that we create appear in the alert. This also makes it easier for investigation.
</p><br>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*JqlZNy3ShtQXk6iI07PvIw.png" height="80%" width="80%" alt=""/>
 <br> How often the query is run and when to look up the data
</p><br>
<p>- Skip Automated Response & Click "Review + create"</p>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*NqLtaXhh9r2t0AeqPqER6g.png" height="80%" width="80%" alt=""/>
 <br> 
</p><br>
<p>- Click "Create" & Now go back to your vm and create another scheduled task and wait for an alert to trigger in Sentinel.
- Go to your Incidents tab to go look for your alerts.</p>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*z6Dc6w0D6TNm7hvZUdSg2w.png" height="80%" width="80%" alt=""/>
 <br> How my incident dashboard looked before I created my alert.
</p><br>
<p>- It may take couple minutes to appear in Sentinel. Keep refreshing!</p>
<p align="center">
<img src="https://cdn-images-1.medium.com/max/1600/1*6qZw6fZug2YaL2i7mz1w5g.png" height="80%" width="80%" alt=""/>
 <br> An alert has been triggered!
</p><br>
<p>- Click the alert and the side bar on the right is where we will begin our investigation. An analyst will investigate with EDR solutions and other tools to decide if it's a true or false positive.<br><br>

You made it to the end!! Now you know what analysts look for in order to start their investigation.<br><br><br>


*I want to give thanks to Cyberwox for introducing this project and allowing me to get practice as an Analyst.*</p>


<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
