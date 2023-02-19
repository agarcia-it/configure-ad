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

- Create Virtual Machines
- Install Active Directory Domain Services
- Join Client to Domain
- Generate Random Users
- Remote Desktop into Client-1 With Any Domain User

<h2>Deployment and Configuration Steps</h2>

<p> 1.) <strong>Create virtual machines - </strong>Within Microsoft Azure, create a Resource Group to house two vm's. The first vm will run Windows Server and should be named DC-1 (DC stands for domain controller in this case). Ensure that a virtual subnet is created with the deployment of DC-1. The second vm should run Windows 10 and should be named Client-1. Ensure that Client-1 is on the same subnet that was created with DC-1. Lastly, make sure to change the Private IP address for DC-1 is set to "Static". This can be done by navigating to your resource group -> DC-1 NIC -> IP Configurations -> Clike Private IP address -> Change Assignment from Dynamic to Static.
</p>
<p align="center">
<img src="https://i.imgur.com/f8hq6tQ.png" height="80%" width="80%" alt="Static IP Address" />
</p>
<br /><br />
<p>DC-1's settings should look like this (Your Public and Private IP addresses will probably be different).</p>
<br /><br />
<p align="center">
<img src="https://i.imgur.com/f9AmTOv.png" height="80%" width="80%" alt="DC-1 Settings" />
</p>
<br /><br />
<p>Client-1's settings should look like this (Your Public and Pricate IP addresses will probably be different).</p>
<br /><br />
<p>
<p align="center">
<img src="https://i.imgur.com/TITFM1z.png" height="80%" width="80%" alt="Client-1 Settings" />
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> 2.) <strong>Ensure connectivity between DC-1 and Client-1 - </strong> Remote Desktop into DC-1. In the search bar, type "firewall". Open Windows Defender Firewall -> Advanced Settings -> Inbound Rules -> Highlight the 4 Inbound Rules that start with "Core Networking Diagnostics - ICMP Echo Request..." and click Enable Rule. Ensure that they have all have a green check mark next to their names. Next, Remote Desktop into Client-1 and open Command Prompt. In Command Prompt, type: "ping 10.0.0.4" and hit Enter (note that 10.0.0.4 is the Private IP address for DC-1, be sure to enter the Private IP address for your DC-1 as it may be different than mine). If you recieve successful replies from DC-1, everything is working properly so far.
</p>
<p align="center">
<img src="https://i.imgur.com/1T0Q0DV.png" height="80%" width="80%" alt="Enable ICMP Traffic" />
</p>
<p align="center">
<img src="https://i.imgur.com/TwlkSVT.png" height="80%" width="80%" alt="Ping DC-1" />
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> 3.) <strong>Install Active Directory Domain Services on DC-1 - </strong>Remote Desktop back in to DC-1. Open up the Server Manager if it isn't already. Click "Add Roles and Features" -> Next x3 -> Select the "Active Directory Domain Services -> Add Features -> Next x2 -> Install. Next, in Server Manager, click the flag with exclamation point at the top right of the window and click "Promote this server to a domain controller". A pop-up window will appear. Select "Add new forest" and then choose a domain name for the server (I chose "mydomain.com"). Click Next and then create a password. Click Next until you can click Install and click Install. This will initiate the process of promoting DC-1 into a domain controller. DC-1 will restart and you will need to Remote Desktop back in to DC-1. You will need to log back in to DC-1 within the context of it being a domain controller. My username in this case is "mydomain.com\aric" (yours will be "mydomain.com\*yourname*").
</p>
<p align="center">
<img src="https://i.imgur.com/4SG6A1B.png" alt="Login to DC-1 In The Context of mydomain.com" />
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> 4.) <strong>Create an Admin and Normal User Account in DC-1</strong>Once logged back in to DC-1, open Server Manager if it isn't already open. Click Tools  in the top-right of Windows Server and select Active Directory Users and Computers. Once inside: right-click "mydomain.com" -> hover over New -> click Organizational Unit and name it "_ADMINS". Repeat this process and create another Organizational Unit called "_EMPLOYEES". Right-click the _ADMINS folder -> hover over New -> click User. Here we will create our admin account for the domain controller. It is good practice to do this as opposed to using a generic user account. We will use Jane Doe as the admin account, with the User Logon name jane_doe. Create a password and uncheck the "User must change password at next login" box, and check the "Password never expires" box and click Next and Finish. Click into _ADMINS -> Right-click Jane Doe -> Properties -> click Member of -> Add -> type domain -> click Check Names -> Select Domain Admins. Click OK -> Apply -> OK and Jane Doe is now an admin for the domain controller. From here on out, we will operate DC-1 with the mydomain.com\jane_admin account.
</p>
<p align="center">
<img src="https://i.imgur.com/aTHP4rs.png" height="80%" width="80%" />
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> 5.) <strong>Join Client-1 to mydomain.com - </strong>From the Azure Portal: go to the Resource Group and click on the NIC for Client-1 -> Click DNS Servers -> Custom -> type in the Private IP address for DC-1 and click Save. Remain in Azure and navigate to Client-1 and restart the vm, this will refresh the DNS server for Client-1. Log back in to Client-1 with the original credentials and open up Command Prompt. Type "ipconfig /all" and ensure the DNS server has is now the Private IP address for DC-1. If it is: Right-click the Windows Icon on the bottom-left -> System -> Rename this PC (advanced) -> Change -> Under Member Of click Domain and enter the domain that was created (mydomain.com in this case) -> Click OK -> A popup window will appear where you can enter the credentials for jane_admin (mydomain.com\jane_admin & the password you chose). If successful, a popup window will appear that says "Welcome to the mydomain.com domain". Client-1 will need to restart, but is now a member of the mydomain.com domain and any users under the mydomain.com domain can login to it. To ensure that Client-1 is a member of the domain, Remote Desktop into DC-1 -> Server Manager -> Tools -> Active Directory Users and Computers -> Highlight mydomain.com -> Double-click the Computers directory, and Client-1 should be there. For ease of access, create a new Organizational Unit called "_CLIENTS" and drag Client-1 inside of there.
</p>
<p align="center">
<img src="https://i.imgur.com/ndgJ1cW.png" height="80%" width="80%" />
</p>
<p align="center">
<img src="https://i.imgur.com/yCYj9BW.png" height="80%" width="80%" />
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> 6.) <strong>Setup Remote Desktop for non-administrative users on Client-1 - </strong>Remote Desktop back in to Client-1 with the username "mydomain.com\jane_admin" and jane_admin's password. Right-click the Windows icon on the bottom-left of the screen -> System -> Remote Desktop -> Select users that can remotely access this PC -> Add -> type "domain" -> Click Check Names -> Select Domain Users -> OK x3. Now, any non-administrative user who is a member of mydomain.com will be able to Remote Desktop into Client-1. This is typically done with Group Policy to change many computers at once, but because only 1 client is used for this lab Group Policy was not used.
</p>
<p align="center">
<img src="https://i.imgur.com/ehJw5bk.png" />
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> 7.) <strong>Create 500 random users - </strong>This setp will serve as a proof of concept to simulate an organization with many users operating within mydomain.com. Navigate to <a href="https://github.com/agarcia-it/configure-ad/blob/main/create_500_random_users.ps1">https://github.com/agarcia-it/configure-ad/blob/main/create_500_random_users.ps1</a> and copy the raw contents of the Powershell script. Login to DC-1 as jane_admin and open Windows Powershell ISE as an administrator. Click New and paste the script into the field. Click Run Script and 500 randomly generated users will be created. All usernames will are structured on a firstname.lastname basis with the password "Password1" for all of them. This means that we can use any of these randomly created credentials in order to login to Client-1 because they are all users operating within mydomain.com - simulating an environment where any members (or employees) can login to any machine on the network.
</p>
<p align="center">
<img src="https://i.imgur.com/SsIGjrO.png" height="80%" width="80%" />
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> 8.) <strong>Login to Client-1 with a random user - </strong>While in DC-1: Open Windows Server Manager -> Tools -> Active Directory Users and Computers -> double-click the _EMPLOYEES directory. This directory contains all of the 500 newly created users that were made. Right-click any user -> Properties -> Account -> highlight and copy the "firstname.lastname" username. Open a new Remote Desktop session and enter the username "mydomain.com\firstname.lastname" and password "Password1". You should be able to login to Client-1 with this random user - and with any of the other randomly created user in the _EMPLOYEES directory on DC-1. 
</p>
<p align="center">
<img src="https://i.imgur.com/FSjiK8W.png" />
</p>
<p align="center">
<img src="https://i.imgur.com/DQcns0C.png" height="60%" width="60%" />
</p>
<p>---------------------------------------------------------------------------------------------------------------------------------</p>
<br />

<p> 9.) <strong>Verify the user and host - </strong>Finally, as a redundant step to verify that the random user was able to login successfully, open the Command Prompt for Client-1 and type "whoami" and hit enter. You should see the random user's name that you selected. Next, type "hostname" and hit enter. you should see that the host machine that you are using is in fact Client-1. This concludes this tutorial, and congratulations on setting up Active Directory Users and Computers! 
</p>
<p align="center">
<img src="https://i.imgur.com/F0x9eYs.png" height="80%" width="80%" />
</p>
