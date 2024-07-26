---
title: Building your own Vulnerable AD LAB
date: 2024-07-25 12:00:00 -500
author: Coppertop
categories: [Blog]
tags: [Active-Directory,Labs]     #   TAG should always be lowercase

---

Ever wondered what it would be like to build an Active Directory (AD) so frail it practically waves a white flag? Well, you're in luck! In this post, we’ll guide you through constructing a small but delightfully vulnerable AD that might as well come with its own set of hacker welcome mats. So, grab your popcorn and get ready to dive into the world of easily exploitable networks, where the goal is not just to build, but to build badly! Today, we'll ensure your AD is as robust as a sandcastle at high tide, vulnerable to the following attacks:

* Abusing ACLs/ACEs
* Kerberoasting
* AS-REP Roasting
* Abuse DnsAdmins
* Password in Object Description
* User Objects With Default password (Changeme123!)
* Password Spraying
* DCSync
* Silver Ticket
* Golden Ticket
* Pass-the-Hash
* Pass-the-Ticket
* SMB Signing Disabled

## Setting up AD

Start by spinning up your Server VM with Microsoft Server 2019 or 2022. Once it's up and running, follow these steps to get your AD environment prepped for its inevitable downfall.

```powershell
Install-windowsfeature AD-domain-services
```


![Install AD](/assets/img/1.png)



Once completed run the following powershell command to run the DC promo process.

```powershell
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath "C:\\Windows\\NTDS" -DomainMode "7" -DomainName "domain.local" -DomainNetbiosName "domain" -ForestMode "7" -InstallDns:$true -LogPath "C:\\Windows\\NTDS" -NoRebootOnCompletion:$false -SysvolPath "C:\\Windows\\SYSVOL" -Force:$true
```
You will be prompted for a DSRM password (Directory Services Restore Mode) and then PS will do the rest :)

Reboot your server and log back in to make sure everything has been setup as expected.

## Configuring the Vulnerable AD Service via PS Script

Run the below powershell commands as admin. To understand what is being setup have a look at the vunlad.ps1 file. (https://github.com/safebuffer/vulnerable-AD/blob/master/vulnad.ps1)

```powershell
IEX((new-object net.webclient).downloadstring("https://raw.githubusercontent.com/wazehell/vulnerable-AD/master/vulnad.ps1"));
Invoke-VulnAD -UsersLimit 100 -DomainName "domain.local"
```

The script will add users, groups, SPN's etc to your domain.

![Run AD script](/assets/img/2.png)

![Run AD script](/assets/img/3.png)

## Install below features to see AD Tools

For easy of AD use you can install the below features on your server.

![Install AD Tools](/assets/img/4.png)

![Install AD Tools](/assets/img/5.png)


## **Additional manual configurations**

## Configuring your File Server (Opening SMB)

Enable SMB file sharing and ensure ports 445 and 139 are open for future lab exercises.
1. Launch Server Manager and navigate to “Dashboard” > “File and Storage Services” > “Shares”.
2. Select “New Share” > “SMB Share — Quick” and proceed by clicking “Next”.
3. Name the share “hackme” and follow the instructions to complete the share setup.

![Configure SMB](/assets/img/6.png)

## Creating Service Principal Name (SPN)

Set up for a Kerberoasting attack.

1. Open the Command Prompt as an administrator.
2. Use the following command to set the SPN:
```cmd 
setspn -a myDomainController/SQLService.domain.local:60111 domain\SQLService.
```
3. Check if the SPN is set using the command:
```cmd
setspn -T domain.local -Q */* | findstr SQL
```
![Configure SPN](/assets/img/7.png)

## Connecting Machines to your Domain

Follow these steps on the machines you want to join to the domain (MS01 and MS02):

1. Set up network configurations. Open network & internet settings, adjust adapter options, and go to IPv4 > Preferred DNS Server. Input the Domain Controller's IP address.
2. Join the domain. Navigate to “Access work or school” > “Connect” > “Join this device to local Active Directory domain”. Enter the domain name as domain.local. Use the "support" account with the password "P@$$w0rd" to join. (Ensure this account is created on the Domain Controller with administrative rights.)
3. Restart the machine.

![Connect machines to the domain](/assets/img/8.png)

4. After restarting, select "Other user" and log in as the domain user of your choice.
5. Assign local administrator rights:
• Open “Computer Management” > “Local Users and Groups” > “Groups”..
• Double-click on “Administrators”.
• Add the user (e.g., Kenny), verify the name, apply, and click OK.

6. Turn on Network Discovery:
• Navigate to “Computer” and then “Network”.
• Click OK when prompted and click “Turn on network discovery…” in the top menu bar.

7. Verify that both computers have joined the domain:
• On the Domain Controller, open “Active Directory Users and Computers” > domain.local > "Computers".
• Verify if both computers are added.

## Create a folder and set up a network share.

1. On the first machine, give the first domain user (Kenny) local administrator rights. (MS01) 
2.  On the second machine, give both domain users (Kenny, Rick) local administrator rights. (MS02)
3.  Create a network share folder.
4.  Create a folder named “YFO” and “Morty” in the C: drive.
5.  Right-click on the folder, go to “Properties” > “Sharing” > “Share” > “Share” > “Yes, turn on network discovery…”.


## Set GPO to diable AV

Disable Windows Defender for lab purposes 
1. Open Group Policy Management as an administrator.
2. Expand the Forest > Domains > domain.local.
3. Right-click and create a new Group Policy Object (GPO) named “Disable Windows Defender” in this domain.
4. Edit the newly created GPO.
5. Navigate to “Computer Configuration” > “Policies” > “Administrative Templates” > “Windows Components” > “Windows Defender Antivirus”.
6. Double-click on “Turn off Windows Defender Antivirus” and enable the policy.

![Configure GPO](/assets/img/9.png)

# Closing Remarks

Congratulations, you've built the most inviting AD lab a hacker could dream of! Now, it's time to unleash your inner cyber wizard. Go forth, test your skills, and hack away at your lab. Just remember, with great power comes great responsibility – and a lot of really funny error messages! Happy hacking!

References 

* https://github.com/safebuffer/vulnerable-AD/
* https://sethsec.blogspot.com/2017/08/pentest-home-lab-0x3-kerberoasting.html
