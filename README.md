<h1>Azure SIEM project </h1>

<h2>Description</h2>
We are going to Setup azure SENTINEL which is Microsoft azure SIEM (Security Information and Event Management tool) with a honeypot to monitor realtime Cyber attacks. We are going to then take those attacks and logs and display them on a world map to show Source geographical locations of these attacks. Using powershell to extract metdata  from windows into a third party API, Configure log analytics into azure workspace to get geographical locations, and configure Azure sentinel workbook to display RDP / attack data on a world map.
<br />


<h2>Languages and Utilities Used</h2>

- <b>Azure/Microsoft Sentinel</b> 
- <b>Powershell</b>

<h2>Environments Used</h2>

- <b>Windows 10</b>
- <b>Azure</b>
- <b>Honeypot</b>

<h2>Skills:</h2> 

- <b>Setting up and creating a honeypot for cyber attacks</b>
- <b>Logging (RDP) and analyzing attacks in microsoft sentinel</b>
- <b>Powershell to extract meta data from windows event viewer with a third part API for geograpical data to log</b>

<h2>Lab:</h2> 

<p>make an Azure Account to get started. Then we are going to create a VM and disable the external and windows firewall to essentially create an Easy honeypot for attackers so we can get some data going. </p>
<p> First Create a virtual machine  and name the resource group Honeypotlab. We will be putting everything under this resource group for deletion convenience later on. Use the setup below</p> 
<img src="https://imgur.com/knYlKAd.gif"/>

<p>Then we go to the networking VM setup after skipping disk setup(leave as defaults). In the NIC setup security group go to advanced and create new. Delete the inbound rules and setup like so and Add. Hit OK to go back to networking. Select review and create/ create again in the bottom left to starup our honeypot VM. Obviously this is just for lab purposes and we wouldnt do this in a real enviornment!</p>
<img src="https://imgur.com/gIpBSiE.gif"/>
  
<p>Now we are going to create a "logs analytics workspace" in azure as our log repository which we can use to ingest/extract logs from our VM.Remember when creating to put under our honeypot resource group. Name it whatever you like for instance details. Review+create/Create</p>
<img src="https://imgur.com/SHGiWs8.gif"/>

<p>The next step is going to Microsoft defender for cloud so we can get logs from the VM into the log analytics workspace. Go into environment settings and click on the log analytics workspace we just made. You may need to refresh or wait for the full deployment for it to show. Servers ON SQL servers OFF Save, this may take a min. Go to data collection and choose all events save.</p>

<p>Back to log analystics workspace and the one we created. Go to virtual machines and Click on our honeypot then connect.</p>
<img src="https://imgur.com/cU2jMaq.gif"/>

<p>Next we are going to setup microsoft sentinel(SIEM) to be able to map all this data to a geograpical map with the help of Powershell. ( I opened another tab for convenience while the VM connected to our log analytics workspace. Simple create a new microsoft sentinel and add our log analytics to it easy. Now lets go see if we can log into our VM if it is done deploying. Get the public IP address from the VM and then we are going to use remote desktop connection on your physical computer.I personally go into options and change the display size so its not my whole monitor for ease of use. Connect with the public IP and go to more choices if you have that option to login the the credentials you created at the beginning for the VM.If not simply just login ez. Accept the certificate warning and we should be in on our VM! nizeee. Say no to all microsoft setup options bc they arent needed. Say yes to Network connection on the side if prompted</p>
<img src="https://imgur.com/baCywCp.gif"/>

<p>Now go to event viewer. Open windows logs/secruity and we are going to be focusing on Audit failures. We can create some of these by going to our Physical computer and using remote desktop connection and intentional do false passwords for logins. I did it a few times to create Failed audit in Event viewer </p>
<img src="https://imgur.com/LZJ1MJ2.gif"/>
<img src="https://imgur.com/fGkaTA3.gif"/>

<p>Now we are going to disable the firewall in our VM so more people can try to get into it on the web. We want all the event thingz to happen so we can plot them out geographically later. "wf.msc" search in your taskbar will give you the advanced firwall settings. Go to windows defender firewall Properties and turn off the Domain, private, and public profiles. Hit apply ok to turn everything off. BE CAREFUL TO DO THIS ONLY IN VM not on your actual PC </p>
<img src="https://imgur.com/37LlU7I.gif"/>

 <p> Go Here to get a custom Powershell log exporter for the information we will need to get for SIEM later https://github.com/joshmadakor1/Sentinel-Lab/blob/main/Custom_Security_Log_Exporter.ps1 Open powershell ISE as admin on your VM so we can run scripts. New, paste the script we just got and save it on desktop as log_exporter. Now go to https://ipgeolocation.io/ on your VM. THis will help us Translate the IP address over to actual locations in the world. Signup on your VM and go to your dashboard. Get your API Key and place that in the API Key field on the Log Exporter file for powershell named $API_KEY. Save the file  </p> 
<img src="https://imgur.com/Kwqc05A.gif"/>

<p>The power shell script is not of my creation and HUGE SHoutout to Josh Madakor for making it. Essentially this script allows you to get event meta data from event viewer in windows (IPaddresses) and get the Geographical data from the 3rd party API ipgeolocation. Then it creates a logfile in "C:\ProgramData\ if one doesnt exist in the VM which will then be used in log analytics to create a custom based log for out SIEM (microsoft sentinel) to map out the locations on a World Map. Run the script in powershell. The script goes through event viewer and looks for the failed logins in real time (event ID 4625)</p>
<img src="https://imgur.com/kaX8Uhn.gif"/>
  
<p>Now that the new custom log is setup in our VM we need to go into azure and create another for our log analytics workspace.We need to copy the content of our Failed RDP log onto our actual PC so we have some data to use within azure log analystics. Go to your VM ctrl+A Ctrl+C and copy the content of the failed RDP in C:\programData in your VM to your Desktop on your actual PC in a new txt document or not pad saved as failed_RDP.log and save Type as All files. This will be used to train log analytics what to look for. Now go into our log analystics in azure. Select tables>create>new custom log MMA Based. Now we are going to use the File we just created on our desktop for the sample log. make sure the record delimiter looks like the file information we need. Set the collection path that was Created in our VM which is C:\ProgramData\failed_rdp.log . In Details name it FAILED_RDP_WITH_GEO and create. This will take alil while to sync. You can check by going to logs in your log analytics and typing FAILED_RDP_WITH_GEO in query to see if it shows. You can also type in SecurityEvent to see that data is being pulled from your event manager in your VM </p>
<img src="https://imgur.com/lJPlTaN.gif"/>
<img src="https://imgur.com/Fe8bewX.gif"/>
<img src="https://imgur.com/wVZiFSU.gif"/>


<p>After a little bit of waiting you will see that our  Failed rdp log will start to return data </p>
<img src="https://imgur.com/vFDxbOZ.gif"/>

<p>Next we will Create the map for sentinel to represent all this data. In microsoft sentinel Go to workbooks>add workbook>edit>add at the bottom left>add query>   Then Copy and run this Query. CREDIT TO: felixcalderon7258 HELLA Appreciated! This query will seperate the raw data into custom fields as Azure no longer supports custom field creation/extraction. Now we have to query and parse the fields this way.</p>
<img src="https://imgur.com/Qp3cJpg.gif"/>


<p>Make sure to delete all resources after the lab to not eat up your free Money given during the trial period!</p>

<p></p>
