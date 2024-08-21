---
title: Azure Front Door as a C2 Redirector (Cloud Domain Fronting using Azure CDN)
date: 2024-08-22 12:00:00 -500
author: h3ll0clar1c3
categories: [Blog]
tags: [Red Team,C2, Azure]     #   TAG should always be lowercase

---

Azure Front Door is a robust service for improving the availability and performance of your applications by providing global load balancing and application acceleration. 
You can use Azure Front Door to handle redirects, which is particularly useful for scenarios like URL rewriting, domain migrations, or managing different environments.

Here’s a basic guide on how to set up Azure Front Door to function as a redirector:

### 1. **Create an Azure Front Door Instance**

   **Log in to the Azure Portal**: Navigate to the Azure Portal (https://portal.azure.com).
   **Create a Front Door**: Go to "Create a resource" and search for "Front Door". Click on "Front Door" and then "Create".
   **Configure Front Door**:
   - **Subscription and Resource Group**: Select your subscription and create or choose a resource group.
   - **Front Door Name**: Enter a unique name for your Front Door.
   - **Frontend Hosts**: Define the frontend hostname (e.g., `www.example.com`).

### 2. **Configure Backend Pools**

   **Add Backend Pools**: Specify the backends where the traffic will be directed. For redirection purposes, this may point to an endpoint that will handle the redirect logic or a web server.
   **Add Backends**: Enter the backend details like the hostname, and configure health probes to ensure your backend is reachable.

### 3. **Set Up Routing Rules**

   **Create Routing Rules**: Go to the "Routing rules" section and create a new rule.
   - **Name**: Enter a name for the rule.
   - **Frontend**: Choose the frontend hostname you configured earlier.
   - **Backend Pool**: Select the backend pool. This will be the target for the redirect or the service handling the redirection logic.
   - **Path Pattern**: Define the path pattern that triggers this rule (e.g., `/*` for all paths).

### 4. **Configure Redirects**

   **Configure URL Rewrite**: In the routing rules, you can set up URL rewrites to handle the redirection.
   - **Redirect Type**: Choose between `Permanent Redirect (301)` or `Temporary Redirect (302)`.
   - **Redirect Destination**: Define the target URL where requests should be redirected. This can be another domain or path.

### 5. **Custom Domain and SSL (Optional)**

   **Add Custom Domains**: If you are using a custom domain, add it under "Frontend hosts" and validate the domain ownership.
   **Configure SSL/TLS**: Set up SSL/TLS for secure connections. You can use a custom certificate or Azure Front Door’s managed certificate.

### 6. **Review and Create**

   **Review Settings**: Ensure all configurations are correct.
   **Create Front Door**: Click "Create" to deploy your Front Door instance with the redirect configurations.

### Example Scenario: Redirecting HTTP to HTTPS

To redirect all HTTP traffic to HTTPS:

   **Frontend Host**: Set up a frontend host for HTTP traffic.
   **Routing Rule**: Create a rule to match HTTP requests.
   **Redirect Configuration**:
   - **Redirect Type**: Permanent (301)
   - **Redirect Destination**: Use HTTPS schema (e.g., `https://{hostname}{path}`).

### Troubleshooting and Monitoring

   **Monitor Traffic**: Use Azure Monitor and Front Door diagnostics to track traffic and redirect performance.
   **Test Redirects**: Ensure that redirects are functioning as expected by testing various scenarios.

Using Adri's write-up as a template ... remove all content from this point onward >

Using Azure Front Door as a redirector helps simplify complex routing rules and provides a global edge network to ensure fast and reliable redirects.

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

Run the below powershell commands as admin. 

To understand what is being setup have a look at the vunlad.ps1 file. (https://github.com/safebuffer/vulnerable-AD/blob/master/vulnad.ps1)

```powershell
IEX((new-object net.webclient).downloadstring("https://raw.githubusercontent.com/wazehell/vulnerable-AD/master/vulnad.ps1"));
Invoke-VulnAD -UsersLimit 100 -DomainName "domain.local"
```

The script will add users, groups, SPNs etc to your domain.

![Run AD script](/assets/img/2.png)

![Run AD script](/assets/img/3.png)

## Install below features to see AD Tools

For easy of AD use you can install the below features on your server.

![Install AD Tools](/assets/img/4.png)

![Install AD Tools](/assets/img/5.png)


## **Additional manual configurations for funzies**

## Configuring your File Server (Opening SMB)

Enable SMB file sharing and ensure ports 445 and 139 are open for future lab exercises.
1. Launch Server Manager and navigate to “Dashboard” > “File and Storage Services” > “Shares”.
2. Select “New Share” > “SMB Share — Quick” and proceed by clicking “Next”.
3. Name the share “hackme” and follow the instructions to complete the share setup.

![Configure SMB](/assets/img/6.png)

## Creating a Service Principal Name (SPN)

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

## Connect two Machines to your Domain

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
• On the Domain Controller, open “Active Directory Users and Computers” > domain.local > "Computers".
• Verify if both computers are added.

## Create shares on your workstations. IYKYK :)

1. On the first machine, give the first domain user (Kenny) local administrator rights. (MS01) 
2.  On the second machine, give both domain users (Kenny, Rick) local administrator rights. (MS02)
3.  Create a network share folder.
4.  Create a folder named “YFO” and “Morty” in the C: drive.
5.  Right-click on the folder, go to “Properties” > “Sharing” > “Share” > “Share” > “Yes, turn on network discovery…”


## Set GPO to disable AV

Disable Windows Defender because ain't nobody got time for that.
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
