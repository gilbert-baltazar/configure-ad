<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Active Directory Deployed in the Cloud (Azure)</h1>
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

- Step 1. Setup Resources in Azure
- Step 2. Ensure Connectivity between the client and Domain Controller
- Step 3. Install Active Directory
- Step 4. Create an Admin and Normal User Account in AD
- Step 5. Join Client-1 to your domain (mydomain.com)
- Step 6. Setup Remote Desktop for non-administrative users on Client-1
- Step 7. Create a bunch of additional users and attempt to log into client-1 with one of the users

<img width="1312" alt="Screen Shot 2024-05-21 at 1 40 49 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/06357105-55c3-4617-b83d-b1cd15ec9328">
<img width="1315" alt="Screen Shot 2024-05-21 at 1 42 56 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/24f360e7-a0eb-4226-8fa9-dbf1d6927bfc">

1. Setup Resourses in Azure 
 - Create a resourse group
 - Create the Domain Controller VM (Windows Server 2022) named “DC-1”
Take note of the Resource Group and Virtual Network (Vnet) that get created at this time

<img width="1315" alt="Screen Shot 2024-05-21 at 1 54 26 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/2a9b36c6-45e5-4a46-b984-a5a589702e9b">

 - Set Domain Controller’s NIC Private IP address to be static
 - Virtual Machines -> DC-1 -> Networking -> Network Interface -> IP configurations -> ipconfig1 -> change from Dynamic to Static -> Save

<img width="1315" alt="Screen Shot 2024-05-21 at 1 49 30 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/2272e0ae-e626-4255-bb5b-0aa7e48a401d">
 - Create the Client VM (Windows 10) named “Client-1”. Use the same Resource Group and Vnet that was created in DC-1
 - Ensure that both VMs are in the same Vnet (you can check the topology with Network Watcher

Note: make sure you let DC-1 VM finish deploying, otherwise, when creating the second VM, it wont show up.

<img width="2088" alt="Screen Shot 2024-05-21 at 2 01 17 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/45fb36f2-d70f-4dc0-970a-409c18f7a6dd">



2. Ensure Connectivity between the client and Domain Controller
 - Login to Client-1 with Remote Desktop and ping DC-1’s private IP address

<img width="999" alt="Screen Shot 2024-05-21 at 2 46 07 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/78e06dd2-2cbb-4a88-ae8f-d1506a6d53d2">

 - Open Command Line -> type in pinng -t (perpetual ping) and the private IP address -> enter.
 - Observe how the comman did not work

<img width="1083" alt="Screen Shot 2024-05-21 at 2 54 38 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/3f9325da-b199-4e44-a97e-ff4718641648">

 - Login to the Domain Controller and enable ICMPv4 in on the local windows Firewall
 - Minimize Client-1 VM window and go to DC-1 VM
 - Once inside DC-1 VM go to finder and type in "Windows Defender firewall with advance security" and open it and expand it

<img width="1592" alt="Screen Shot 2024-05-21 at 2 58 17 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/9049022c-ad87-4a4e-8046-f86a79f7d963">
<img width="1592" alt="Screen Shot 2024-05-21 at 2 59 19 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/812bd19d-e32c-4062-ac49-39e8b2640c53">


 - Click on Inbound Rules -> Protocol -> look for "Core Networking - Destination unreachable Fragmentation Needed (ICMPv4)
 - Right click on the 2 rules directly under it and click "enable rule"

<img width="998" alt="Screen Shot 2024-05-21 at 3 04 31 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/113a205c-bb35-49f4-bfa3-c7b913d45f4e">

 - Minimize DC-1 VM and go back to Client-1 VM
 - Observer how the ping started working again after enabling the ICPv4 request on the windows firewall.
 - Press Control C and close the command line window.

<img width="1488" alt="Screen Shot 2024-05-21 at 2 51 27 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/1d40b455-fcdb-4fd1-b121-ca57d385b865">

<img width="1489" alt="Screen Shot 2024-05-21 at 3 16 20 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/6ecbf084-8031-4593-a1ec-eecf8fb31a50">

3. Install Active Directory
 -  Minimize Client 1 VM and Go to DC-1
 -  Open up Server manager
 -  Click Add roles and features -> click next until you get to Server Roles -> Click on "Active Directory Domain Services" -> Add feature -> click next until it says install -> install -> click close after its done

<img width="1486" alt="Screen Shot 2024-05-21 at 3 20 50 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/6dcb1009-c5f8-4ab6-a415-b9b997e52af5">

 -  Click on the Yellow exclamation point icon next to the flag on top
 -  Click on "Promote thi server to a domain controller"

<img width="1487" alt="Screen Shot 2024-05-21 at 3 24 11 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/6a7d45f6-0ceb-4937-929a-b2b838061573">


 -  Click on Add a new forest
 -  Type in "mydomain.com"
 -  click next to type in a password and put which ever password you choose
 -  click next to everything and install

<img width="1237" alt="Screen Shot 2024-05-22 at 3 06 20 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/21c9ef9c-52c3-4e95-b06a-9828ab5615ec">

 - After it finishes installing, it be automatically sign you out

<img width="771" alt="Screen Shot 2024-05-21 at 3 34 17 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/c6a89255-ff7a-4fb3-9239-93447b076208">

 - Open it back up but instead of using the username you originally put in for the VM, type in the username you put in the root domain name \ your VM username
 - In this case, it is mydomain.com\Derek15. Reason for this is because we turned this VM into a Domain controller

<img width="775" alt="Screen Shot 2024-05-22 at 4 32 54 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/d29f0574-5dd4-4e01-a22d-5d35719a99bf">
<img width="777" alt="Screen Shot 2024-05-22 at 4 44 11 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/f09a02e8-ece6-4134-bf00-62bdf967d04c">


4. Create an Admin and Normal User Account in AD
 - In DC-1 VM, Open Active Directory Users and Computers (ADUC), create an Organizational Unit (OU) called “_EMPLOYEES”
 - Right Click mydomain.com -> New -> Organizational Unit -> Name it _EMPLOYEES -> Ok
 - Right Click mydomain.com -> New -> Organizational Unit -> name it _ADMIN
 - Right click mydomain.com and click Refresh it

<img width="775" alt="Screen Shot 2024-05-22 at 4 42 33 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/05c24d32-f37a-4d36-80df-a293f2eb0d8d">

 - Right click _ADMIN -> New -> User and name it Jane Doe and set a username and password

<img width="774" alt="Screen Shot 2024-05-22 at 4 50 26 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/6bdd8793-9b4e-4626-9a6c-e2cab3f4692a">

 - After you created the user, go to the _ADMINS folder
 - right click Jane Doe -> properties -> Add -> Type in "domain" -> Check names -> Domain Admin -> Ok -> Apply, then click Ok

<img width="772" alt="Screen Shot 2024-05-22 at 4 54 35 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/b78900ae-f5da-4482-abc5-c16f95e7fe2e">

 - Log out of DC-1 VM and log back in DC-1 VM as the user we just created.

<img width="992" alt="Screen Shot 2024-05-22 at 4 57 37 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/629fb080-9ac7-432b-b6c6-d7e88d372e11">

 - After your logged in, If you want to make sure you are logged in as Jane Doe, Open up command line and type in "whoami" or "hostname" and it user you are logged in as will pop up

5. Join Client-1 to your domain (mydomain.com)
 - Minimize DC-1 VM

<img width="1520" alt="Screen Shot 2024-05-22 at 5 12 38 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/86ffe850-76ed-4eba-a7dc-5c228cd05bee">

 - From the Azure Portal, set Client-1’s DNS settings to the DC’s Private IP address
 - Go to DC-1 VM(in azure), Click networkig and get its private IP
 - Go to Client-1(in azure), Click netwoking -> Network interface -> DNS servers -> click "custom" and put int DC-1's private IP address

<img width="1038" alt="Screen Shot 2024-05-22 at 5 14 39 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/ac437820-52f9-47f9-a46a-3b5c302c4d75">

Go to Client-1 overview page, click Restart

<img width="1244" alt="Screen Shot 2024-05-22 at 5 06 25 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/416633f8-78b0-41e7-b524-12ef43107116">

 - Log back in to Client-1 VM in Microsoft Remote Desktop
 - Right click Start -> System -> Name this PC (Advanced) -> Change -> Click Domain and type in domain.com

<img width="458" alt="Screen Shot 2024-05-22 at 5 19 11 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/12f18789-48b6-4ea1-b863-e848bd422f58">

 - It will ask to type in username and password
 - type in the username and password for DC-1
 - A window will pop up asking if you want to restart the computer now. Click restart now

<img width="769" alt="Screen Shot 2024-05-22 at 5 22 09 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/fcd310d3-0c93-4b07-9414-64b6ffa615f5">

6. Setup Remote Desktop for non-administrative users on Client-1
 - Open Client-1 VM again. Instead of using the credentials you created for Client-1 VM, put in the username and password for DC-1

<img width="1207" alt="Screen Shot 2024-05-22 at 5 28 04 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/b3e487fb-bdc7-4f98-b6df-3b666d7b6ec0">

 - Once inside, right click start -> system -> Remote Desktop -> Select users that can remotely access this PC -> Add -> type in "domain users" -> check names -> Ok


<img width="1218" alt="Screen Shot 2024-05-22 at 5 31 22 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/4814d364-b1d6-44bc-883a-7759f262c504">

 - Minimize Client-1 VM and go to DC-1 VM
 - Open Active directory users and computes and click mydomain.com -> users -> Domain Users -> Members
 - You will see all the user accounts that have been created that are allowed to remotely login to Client-1

<img width="2125" alt="Screen Shot 2024-05-22 at 5 59 44 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/e49ec9f7-0d6e-4f71-a006-23a41d4aabcd">

7. Create a bunch of additional users and attempt to log into client-1 with one of the users
 - In DC-1, Open PowerShell ISE As an administrator (rigth click powershell)
 - Copy the files from this link (https://github.com/DereHz/Generate-Names-Created-Users/blob/main/README.md)
 - Paste it on PowerShell

<img width="2126" alt="Screen Shot 2024-05-22 at 5 51 16 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/3d928b55-faf4-4e66-9ff1-7c51f9665c79">

 - Run the script and observe the accounts being created
 - When finished, open ADUC and observe the accounts in the appropriate OU
 
 
 <img width="1216" alt="Screen Shot 2024-05-22 at 6 08 32 PM" src="https://github.com/DereHz/Configure-Active-Directory/assets/169094076/74b74515-9e87-42d0-9a8a-bd16fb15700c">
 
 - attempt to log into Client-1 with one of the accounts (take note of the password in the script)
 - For example, go to ADUC and click on mydomain.com -> _EMPLOYEES -> pick a random user recently created -> Account -> and copy the username
 - Go to Client-1 and log off of it
 - Log back in with the username of one of the users you picked
 -  mydomain.com\bec.huvo




   You have successfully configured Active Directory



That Concludes This Project. 

