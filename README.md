<h1>Azure SIEM project </h1>

<h2>Description</h2>
We are going to Setup azure SENTINEL which is Microsoft azure SIEM (Security Information and Event Management tool) with a honeypot to monitor realtime Cyber attacks. We are going to then take those attacks and logs and display them on a world map to show Source geographical locations of these attacks. Using powershell to extract metdata  from windows into a third party API, Configure log analytics into azure workspace to get geographical locations, and configure Azure sentinel workbook to display RDP / attack data on a world map.
<br />


<h2>Languages and Utilities Used</h2>

- <b>Azure/Azure Sentinel</b> 
- <b>Powershell</b>

<h2>Environments Used</h2>

- <b>Windows 10</b>
- <b>Azure</b>
- <b>Honeypot</b>

<h2>Skills:</h2> 

- <b>Setting up and creating a honeypot for cyber attacks</b>
- <b>Logging (RDP) and analyzing attacks in azure sentinel</b>
- <b>Powershell to extract meta data from windows event viewer with a third part API for geograpical data to log</b>

<h2>Lab:</h2> 

<p>make an Azure Account to get started. Then we are going to create a VM and disable the external and windows firewall to essentially create an Easy honeypot for attackers so we can get some data going. </p>
<p> First Create a virtual machine  and name the resource group Honeypotlab. We will be putting everything under this resource group for deletion convenience later on. Use the setup below</p> 
<img src="https://imgur.com/knYlKAd.gif"/>

<p>Then we go to the networking VM setup after skipping disk setup(leave as defaults). In the NIC setup security group go to advanced and create new. Delete the inbound rules and setup like so and Add. Hit OK to go back to networking. Select review and create/ create again in the bottom left to starup our honeypot VM. Obviously this is just for lab purposes and we wouldnt do this in a real enviornment!</p>
<img src="https://imgur.com/gIpBSiE.gif"/>
  
<p>Now we are going to create a "logs analytics workspace" in azure as our log repository which we can use to ingest/extract logs from our VM.Remember when creating to put under our honeypot resource group. Name it whatever you like for instance details</p>
<img src="https://imgur.com/SHGiWs8.gif"/>

<p>Next we are going to setup azure sentinel(SIEM) to be able to map all this data to a geograpical map with the help of Powershell. This metadata will be sent to a third party API with the IP address to get the geographical information such as latitude and longitude. We will send this back to our VM to create a custom log with this information. </p>

<p>Make sure to delete all resources after the lab to not eat up your free Money given during the trial period!</p>

<p></p>
