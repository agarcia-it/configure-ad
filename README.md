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

- Step 1
- Step 2
- Step 3
- Step 4

<h2>Deployment and Configuration Steps</h2>

<p> 1.) <strong>Create virtual machines - </strong>Within Microsoft Azure, create a Resource Group to house two vm's. The first vm will run Windows Server and should be named DC-1 (DC stands for domain controller in this case). Ensure that a virtual subnet is created with the deployment of DC-1. The second vm should run Windows 10 and should be named Client-1. Ensure that Client-1 is on the same subnet that was created with DC-1. Lastly, make sure to change the Private IP address for DC-1 is set to "Static". This can be done by navigating to your resource group -> DC-1 NIC -> IP Configurations -> Clike Private IP address -> Change Assignment from Dynamic to Static.
</p>
<p>
<img src="https://i.imgur.com/f8hq6tQ.png" />
</p>
<br /><br />
<p>DC-1's settings should look like this (Your Public and Private IP addresses will probably be different).</p>
<br /><br />
<p>
<img src="https://i.imgur.com/f9AmTOv.png" />
</p>
<br /><br />
<p>Client-1's settings should look like this (Your Public and Pricate IP addresses will probably be different).</p>
<br /><br />
<p>
<p>
<img src="https://i.imgur.com/TITFM1z.png" />
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> 2.) <strong>Ensure connectivity between DC-1 and Client-1 - </strong> Remote Desktop into DC-1. In the search bar, type "firewall". Open Windows Defender Firewall -> Advanced Settings -> Inbound Rules -> Highlight the 4 Inbound Rules that start with "Core Networking Diagnostics - ICMP Echo Request..." and click Enable Rule. Ensure that they have all have a green check mark next to their names. Next, Remote Desktop into Client-1 and open Command Prompt. In Command Prompt, type: "ping 10.0.0.4" and hit Enter (note that 10.0.0.4 is the Private IP address for DC-1, be sure to enter the Private IP address for your DC-1 as it may be different than mine). If you recieve successful replies from DC-1, everything is working properly so far.
</p>
<img src="https://i.imgur.com/1T0Q0DV.png" />
<p>
</p>
<img src="https://i.imgur.com/TwlkSVT.png" />
<p>
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> 3.) <strong>Install Active Directory Domain Services on DC-1 - </strong>Remote Desktop back in to DC-1. Open up the Server Manager if it isn't already. Click "Add Roles and Features" -> Next x3 -> Select the "Active Directory Domain Services -> Add Features -> Next x2 -> Install. Next, in Server Manager, click the flag with exclamation point at the top right of the window and click "Promote this server to a domain controller". A pop-up window will appear. Select "Add new forest" and then choose a domain name for the server (I chose "mydomain.com"). Click Next and then create a password. Click Next until you can click Install and click Install. This will initiate the process of promoting DC-1 into a domain controller. DC-1 will restart and you will need to Remote Desktop back in to DC-1. You will need to log back in to DC-1 within the context of it being a domain controller. My username in this case is "mydomain.com\aric" (yours will be "mydomain.com\*yourname*").
</p>
<img src="https://i.imgur.com/4SG6A1B.png" />
<p>
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> 4.) <strong>Create an Admin and Normal User Account in DC-1</strong>Once logged back in to DC-1, open Server Manager if it isn't already open. Click Tools  in the top-right of Windows Server and select Active Directory Users and Computers. Once inside: right-click "mydomain.com" -> hover over New -> click Organizational Unit and name it "_ADMINS". Repeat this process and create another Organizational Unit called "_EMPLOYEES". Right-click the _ADMINS folder -> hover over New -> click User. Here we will create our admin account for the domain controller. It is good practice to do this as opposed to using a generic user account. We will use Jane Doe as the admin account, with the User Logon name jane_doe. Create a password and uncheck the "User must change password at next login" box, and check the "Password never expires" box and click Next and Finish. Click into _ADMINS -> Right-click Jane Doe -> Properties -> click Member of -> Add -> type admin -> click Check Names (admin should change to Administrators and should be underlined. Click OK -> Apply -> OK and Jane Doe is now an admin for the domain controller. From here on out, we will operate DC-1 with the mydomain.com\jane_admin account.
</p>
<img src="https://i.imgur.com/aTHP4rs.png" />
<p>
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> <strong>Join Client-1 to mydomain.com - </strong>From the Azure Portal: go to the Resource Group and click on the NIC for Client-1 -> Click DNS Servers -> Custom -> type in the Private IP address for DC-1 and click Save. Remain in Azure and navigate to Client-1 and restart the vm, this will refresh the DNS server for Client-1. Log back in to Client-1 with the original credentials and open up Command Prompt. Type "ipconfig /all" and ensure the DNS server has is now the Private IP address for DC-1. If it is: Right-click the Windows Icon on the bottom-left -> System -> Rename this PC (advanced) -> Change -> Under Member Of click Domain and enter the domain that was created (mydomain.com in this case) -> Click OK -> A popup window will appear where you can enter the credentials for jane_admin (mydomain.com\jane_admin & the password you chose). If successful, a popup window will appear that says "Welcome to the mydomain.com domain". Client-1 will need to restart, but is now a member of the mydomain.com domain and any users under the mydomain.com domain can login to it.
</p>
<img src="" />
<p>
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> <strong></strong>
</p>
<img src="" />
<p>
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

