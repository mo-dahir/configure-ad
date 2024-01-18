<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Active Directory Deployed in Azure</h1>
This tutorial guides the implementation of Active Directory within Microsoft Azure Virtual Machines. For this tutorial to be completed, you must finish the prerequisites first and come back to this one as it builds upon those.  <br />

<h2>Technologies and Enviroments Used</h2>

- Microsoft Azure
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Create resources in a resource group in Azure
- Ensure Connectivity between the client and Domain Controller
- Install Active Directory
- Create an Admin and Normal User Account in Active Directory
- Join Client-1 to your domain
- Setup Remote Desktop for non-administrative users on Client-1
- Create additional users and attempt to log into client-1 with one of the users
- Delete resources

<h2>Deployment and Set Up</h2>

<p>
<img src="https://i.imgur.com/p14FRTc.png"/>
</p>
<p>
First, we'll create the resources needed for this tutorial. I will be naming the resource group "AD-RG", the virtual machine "DC-1" and then select the Windows Server 2022. Make sure the size is set to be 2 VCPUs and then create the VM.
</p>
<br />

<p>
<img src="https://i.imgur.com/dp4eYP5.png"/>
</p>
<p>
Secondly, we'll create the Windows 10 VM named "Client-1". We must ensure that it is made under the same resource group, region, and have the same size VCPUs of the first VM. It will also need to be in the same Vnet as "DC-1".
</p>
<br />

<p>
<img src="https://i.imgur.com/eWYjVuc.png"/>
</p>
<p>
Now go into DC-1 and click into the NIC (network interface card) as shown above.
</p>
<br />

<p>
<img src="https://i.imgur.com/1BAgy2q.png"/>
</p>
<p>
Then click into "IP Configurations" and click on "ipconfig1".
</p>
<br />

<p>
<img src="https://i.imgur.com/dQ5OEpW.png"/>
</p>
<p>
Set the IP to static and then click save in the top left.
</p>
<br />

<p>
<img src="https://i.imgur.com/eRMMQo1.png"/>
</p>
<p>
Then login into "Client-1" with RMC and continously ping "DC-1" private IP address with "ping -t". Observe how the request is being timed out and keep this window up.
</p>
<br />

<p>
<img src="https://i.imgur.com/p32Fbkw.png"/>
</p>
<p>
After, log into "DC-1" and open Windows Defender Firewall with Advanced Security by typing it in the search bar.
</p>
<br />

<p>
<img src="https://i.imgur.com/r8Ha3kF.png"/>
</p>
<p>
Then we'll go to inbound rules, sort it by protocol and then enable all the rules with ICMPv4 protocol. This will enable the ICMPv4 traffic that the ping command uses.
</p>
<br />

<p>
<img src="https://i.imgur.com/MM6D6we.png"/>
</p>
<p>
If we return back to "Client-1", we will notice that we are now getting replies back. To stop the ping you can press "Ctrl + C".
</p>
<br />

<p>
<img src="https://i.imgur.com/V7OJEXt.png"/>
</p>
<p>
Finally, we will install Active Directory inside of DC-1. Open up DC-1 and click into "Sever Manager". The first thing we will click is "Add roles and features", press next until we get into "Server Roles", check the box that says "Active Directory Domain Services", click "Add Features" and then continue to click next until you can install.
</p>
<br />

<p>
<img src="https://i.imgur.com/S4wfeU7.png"/>
</p>
<p>
Next, we'll promote the DC-1 as a domain controller. To do this we'll click on the flag with the caution sign on the top right of "Server Manager" and press "Promote this server to a domain controller".
</p>
<br />

<p>
<img src="https://i.imgur.com/FAoWuyu.png"/>
</p>
<p>
Next, check "Add a new forest" name it to anything you want. In this tutorial we will use "mydomain.com" and hit next.
</p>
<br />

<p>
<img src="https://i.imgur.com/lGkKBKx.png"/>
</p>
<p>
Now you can make a DSRM password and hit next until you are able to install. This will restart the virtual machine after it is finished.
</p>
<br />

<p>
<img src="https://i.imgur.com/BNaS4U8.png"/>
</p>
<p>
Next, we'll connect back into "DC-1" with RDC. This time we will use a different account and type the user as "mydomain.com\labuser" or the username that you created when you made "DC-1" The password will be the same.
</p>
<br />

<p>
<img src="https://i.imgur.com/WigUhul.png"/>
</p>
<p>
Now we'll go to the server manager and then go to tools. Press "Active Directory Users and Computers" or ADUC.
</p>
<br />

<p>
<img src="https://i.imgur.com/i43uWce.png"/>
</p>
<p>
Next create two new "Organizational Units" by clicking on mydomain and right clicking the white space. The first unit will be named "_EMPLOYEES" and the second one "_ADMINS".
</p>
<br />

<p>
<img src="https://i.imgur.com/gwWNdCc.png"/>
</p>
<p>
After, we will create a new admin user by clicking to top button with a person and star on it. Name the user Jane Doe and the login username to "jane_admin". In the next screen, uncheck "User must cahnge password at next logon" then hit next to create the user.
</p>
<br />

<p>
<img src="https://i.imgur.com/94VdfZZ.png"/>
</p>
<p>
Next, add Jane Doe to the group "Domain Admins". To perfom this step, right click on Jane > Properties > Member Of > Add > type "Domain" in the object box > Check Names > click "Domain Admins" > click Ok and then apply. After this, you will logout and log on to DC-1 as jane_admin.

</p>
<br />

<p>
<img src="https://i.imgur.com/STzrbqa.png"/>
</p>
<p>
Now set Client-1's DNS server to the private IP of DC-1. To do this, head back to Client-1's nertworking on the Azure portal and click on the NIC. Click into DNS servers and add the private IP of DC-1 into the list and press save. Restart Client-1 by going back to the VM on Azure and press restart button at the top. Log back in after restarting.
</p>
<br />

<p>
<img src="https://i.imgur.com/Td5WTVf.png"/>
</p>
<p>
When inside Client-1, go to Settings > About > Rename this PC(Advanced) > change domain > type mydomain.com > use mydomain.com\jane_admin > then press ok to make the changes apply. Restart Client-1 and log into jane_admin.
</p>
<br />

<p>
<img src="https://i.imgur.com/JreB3FV.png"/>
</p>
<p>
After logging in, go to Remote Desktop Settings and click "Select users that can remotely access this PC" > add > type "domain users" in the object box > check names and then hit OK. This will allow all domain users to connect to Client-1.
</p>
<br />

<p>
<img src="https://i.imgur.com/ZxtMnsR.png"/>
</p>
<p>
Head back to DC-1 and into ADUC in the server manager application. Check to see if Client-1 is listed by clicking into mydomain and computers where you should see Client-1.
</p>
<br />

<p>
<img src="https://i.imgur.com/ENYg8F0.png"/>
</p>
<p>
While in DC-1, open "Windows Powershell ISE (x86)" as admin.
</p>
<br />

<p>
<img src="https://i.imgur.com/sX7oUvb.png"/>
</p>
<p>
Finally, create 10,000 users with a randomly generated names by copying the powershell script linked below. To do this we will create a new script by pressing the button at the top left > paste script > and then hit the green run script button.

- [Script](https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1)

After the users are created, you can use any of them to sign into Client-1. The password is defaulted to "Password1" when the script was ran on powershell.
</p>
<br />
