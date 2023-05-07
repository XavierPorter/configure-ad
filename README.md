<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1: Setup Resources in Azure
- Step 2: Ensure Connectivity between the client and Domain Controller
- Step 3: Install Active Directory
- Step 4: Create an Admin and Normal User Account in AD
- Step 5: Join Client-1 to your domain (mydomain.com)
- Step 6: Setup Remote Desktop for non-administrative users on Client-1
- Step 7: Create a bunch of additional users and attempt to log into client-1 with one of the users

<h1>Deployment and Configuration Steps</h1>

<h2>1. Setup Resources in Azure.</h2>

<p>
<img src="https://i.imgur.com/tvjUVmY.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/3j18xuG.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<ol>
 <li>Create the Domain Controller VM (Windows Server 2022) named “DC-1”</li>
 <li>1.a: **Take note of the Resource Group and Virtual Network (Vnet) that get created at this time</li>
 <li>Set Domain Controller’s NIC Private IP address to be static</li>
 <li>Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in Step 1.a</li>
 <li>Ensure that both VMs are in the same Vnet (you can check the topology with Network Watcher</li> 
</ol>
<br />

<h2>2. Ensure Connectivity between the client and Domain Controller.</h2>

<p>
<img src="https://i.imgur.com/LdNMJFD.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/7s66jli.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  <ol>
    <li>Login to Client-1 with Remote Desktop (IP Address) and ping DC-1’s private IP address with ping -t <ip address> (perpetual ping)</li>
    <li>Login to the Domain Controller (DC-1) and in the start menu, type "wf.msc" -> click inbound files -> Sort by Protocol -> right click and enable ICMPv4 in on the local windows Firewall</li>
    <li>Check back at Client-1 to see the ping succeed</li>
  </ol>
</p>
<br />

<h2>3. Install Active Directory</h2>

<p>
 <img src="https://i.imgur.com/IftiJZD.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
 <img src="https://i.imgur.com/p60G9KV.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  <ol>
    <li>Login to DC-1 and in the Server Manager, click "Add roles and features" and install Active Directory Domain Services</li>
    <li>Click the flag in the top right hand corner to promote as a Domain Controller: Setup a new forest as mydomain.com (can be anything, just remember what it is)</li>
    <li>Restart and then log back into DC-1 as user: mydomain.com\labuser</li>
  </ol>
</p>
<br />


<h2>4. Create an Admin and Normal User Account in AD</h2>

<p>
<img src="https://i.imgur.com/xW1wf8V.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
 <img src="https://i.imgur.com/HGP3vTR.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  <ol>
    <li>In the Sever Manager, click "Tools"</li>
    <li>In Active Directory Users and Computers (ADUC), right click on "mydomain", click "New" and create an Organizational Unit (OU) called “_EMPLOYEES”</li>
    <li>Create a new OU named “_ADMINS”</li>
    <li>Refresh</li>
    <li>Right click in the empty space and click "New" then click "User"</li>
    <li>Create a new employee named “Jane Doe” (same password) with the username of   “jane_admin”</li>
    <li>Right click "Jane Doe" and seleect "Properties"</li>
    <li>Select the "Members Of" tab and click "Add"</li>
    <li>In the empty space, type "domains" and Add jane_admin to the “Domain Admins” Security Group</li>
    <li>Log out/close the Remote Desktop connection to DC-1 and log back in as “mydomain.com\jane_admin”</li>
    <li>Use jane_admin as your admin account from now on</li>
  </ol>
</p>
<br />


<h2>5. Join Client-1 to your domain (mydomain.com)</h2>

<p>
<img src="https://i.imgur.com/3mKCukD.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/tJFPBby.png" height="80%" width="80%" alt="Disk Sanitization Steps"/> 
</p>
<p>
  <ol>
    <li>From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address</li>
    <li>To do that, copy DC-1's private IP address.</li>
    <li>Select the "Networking" section and click the Network Interface</li>
    <li>Click the "DNS Servers" section</li>
    <li>Select the custom option and paste the DC-1's private IP address</li>
    <li>From the Azure Portal, save and restart Client-1</li>
    <li>Login to Client-1 (Remote Desktop) as the original local admin (labuser) and join it to the domain (computer will restart)</li>
    <li>To do this, go to system settings, and click "Rename this PC (advanced)"</li>
    <li>Click "change" and select the domain option and type "mydomain.com" then click "ok"</li>
    <li>Enter the username "mydomain.com\jane_admin" and password "Password1"</li>
    <li>Login to the Domain Controller (Remote Desktop) and verify Client-1 shows up in Active Directory Users and Computers (ADUC) inside the “Computers” container on the root of the domain</li>
    <li>Create a new OU named “_CLIENTS” and drag Client-1 into there</li>
  </ol>
</p>
<br />



<h2>6. Setup Remote Desktop for non-administrative users on Client-1</h2>

<p>
<img src="https://i.imgur.com/rCYqorP.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  <ol>
    <li>Log into Client-1 as mydomain.com\jane_admin and open system settings</li>
    <li>Click “Remote Desktop” and click "Select users that can remotely access this PC</li>
    <li>Allow “domain users” access to remote desktop</li>
    <li>You can now log into Client-1 as a normal, non-administrative user now</li>
  </ol>
</p>
<br />


<h2>7. Create a bunch of additional users and attempt to log into client-1 with one of the users</h2>

<p>
<img src="https://i.imgur.com/DJmEXEB.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
</p>
<p>
  <ol>
    <li>Login to DC-1 as jane_admin</li>
    <li>Open PowerShell_ise as an administrator</li>
    <li>Create a new File and paste the contents of the script into it (https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)</li>
    <li>Run the script and observe the accounts being created</li>
    <li>When finished, open ADUC and observe the accounts in the appropriate OU</li>
    <li>attempt to log into Client-1 with one of the accounts (take note of the password in the script)</li>
  </ol>
</p>
<br />
