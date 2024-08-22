---
title: Azure Front Door as a C2 Redirector (Cloud Domain Fronting using Azure CDN)
date: 2024-08-22 12:00:00 -500
author: h3ll0clar1c3
categories: [Blog]
tags: [Red Team,C2,Azure,Front Door,CDN,Redirector,Mythic] #   TAG should always be lowercase

---

Azure Front Door is a robust CDN (Content Delivery Network) service for improving the availability and performance of your applications by providing global load balancing and application acceleration. 

You can use Azure Front Door to handle redirects, which is particularly useful for scenarios like URL rewriting, domain migrations, or managing different environments.

Using Azure Front Door as a redirector helps simplify complex routing rules and provides a global edge network to ensure fast and reliable redirects.

Here’s a basic guide on how to set up Azure Front Door to function as a C2 redirector:

## 1. **Create an Azure Front Door Instance**

   **Log in to the Azure Portal**: Navigate to the Azure Portal (https://portal.azure.com).

   ![Configure Azure Front Door](/assets/img/10.png)

   **Create a Front Door**: Go to "Create a resource" and search for "Front Door". Click on "Front Door and CDN profiles" and then "Create".

   ![Configure Azure Front Door](/assets/img/11.png)

   ![Configure Azure Front Door](/assets/img/12.png)

   **Configure Front Door**:
   - **Subscription and Resource Group**: Select your subscription and create or choose a resource group.
   - **Front Door Name**: Enter a unique name for your Front Door (e.g., `fd-1`).
   - **Frontend Hosts**: Define the frontend hostname (e.g., `test-dnh9h9fyezd8d5f9.a01.azurefd.net`).
  
  ![Configure Azure Front Door](/assets/img/13.png)

In this example we will not be using a custom domain, so we can proceed without the need to add a secret or SSL certificate.

  ![Configure Azure Front Door](/assets/img/14.png)

## 2. **Configure Backend Pools**

   **Add Backend Pools**: Specify the backends where the traffic will be directed. For redirection purposes, this may point to an endpoint that will handle the redirect logic or a web server.

   **Add Backends**: Enter the backend details like the hostname, and configure health probes to ensure your backend is reachable (e.g., `test-dnh9h9fyezd8d5f9.a01.azurefd.net`).

   ![Configure Azure Front Door](/assets/img/15.png)

   ![Configure Azure Front Door](/assets/img/16.png)

## 3. **Set Up Routing Rules**

   **Create Routing Rules**: Go to the "Routing rules" section and create a new rule.
   - **Name**: Enter a name for the rule.
   - **Frontend**: Choose the frontend hostname you configured earlier.
   - **Backend Pool**: Select the backend pool. This will be the target for the redirect or the service handling the redirection logic.
   - **Path Pattern**: Define the path pattern that triggers this rule (e.g., `/*` for all paths).

 ![Configure Azure Front Door](/assets/img/17.png)

 ![Configure Azure Front Door](/assets/img/18.png)

 ![Configure Azure Front Door](/assets/img/19.png)

## 4. **Configure Redirects**

   **Configure URL Rewrite**: In the routing rules, you can set up URL rewrites to handle the redirection.
   - **Redirect Type**: Choose between `Permanent Redirect (301)` or `Temporary Redirect (302)`.
   - **Redirect Destination**: Define the target URL where requests should be redirected. This can be another domain or path.

 ![Configure Azure Front Door](/assets/img/20.png)

## 5. **Custom Domain and SSL (Optional)**

   **Add Custom Domains**: If you are using a custom domain, add it under "Frontend hosts" and validate the domain ownership.

   **Configure SSL/TLS**: Set up SSL/TLS for secure connections. You can use a custom certificate or Azure Front Door’s managed certificate.

## 6. **Review and Create**

   **Review Settings**: Ensure all configurations are correct.

   **Create Front Door**: Click "Create" to deploy your Front Door instance with the redirect configurations.

   ![Configure Azure Front Door](/assets/img/21.png)

   ![Configure Azure Front Door](/assets/img/22.png)

   ![Configure Azure Front Door](/assets/img/23.png)

   ![Configure Azure Front Door](/assets/img/24.png)

### Example Scenario: Redirecting HTTP to HTTPS

To redirect all HTTP traffic to HTTPS:

   **Frontend Host**: Set up a frontend host for HTTP traffic.

   **Routing Rule**: Create a rule to match HTTP requests.

   **Redirect Configuration**:
   - **Redirect Type**: Permanent (301)
   - **Redirect Destination**: Use HTTPS schema (e.g., `https://{hostname}{path}`).

## Troubleshooting and Monitoring

   **Monitor Traffic**: Use Azure Monitor and Front Door diagnostics to track traffic and redirect performance.
   **Test Redirects**: Ensure that redirects are functioning as expected by testing various scenarios.

## 7. **Configure Azure Front Door Endpoint as a C2 Redirector URL**

Azure Front Door Endpoint hostname/URL `test-dnh9h9fyezd8d5f9.a01.azurefd.net`.

![Configure Azure Front Door](/assets/img/25.png)

**Configure Azure Front Door SSL certificate keychain within C2 Profile**: Add in the Azure Front Door keychain (privkey.pem and fullchain.pem) to the C2 Profile.

![Configure Azure Front Door](/assets/img/31.png)

**Select C2 Profiles**: Set the "Callback Host" within the Mythic C2 framework when creating and configuring your desired payload e.g., Apollo Windows Exe payload utilising the 'http' C2 Profile `https://test-dnh9h9fyezd8d5f9.a01.azurefd.net`.

![Configure Azure Front Door](/assets/img/26.png)

**Trigger Payload**: Transfer the newly created payload to your 'vulnerable' victim/workstation, and run the payload in a command terminal/powershell window.

```powershell
.\apolloCF_443_8001.exe
```

![Configure Azure Front Door](/assets/img/27.png)

![Configure Azure Front Door](/assets/img/28.png)

**Analyse C2 Beacon calling back to the Azure Front Door URL**: Launch Wireshark and listen on your local Ethernet adapter whilst filtering on 'TCP port 443' and 'IP address x.x.x.x' (perform an NS Lookup via DNS to find the IP address of the CDN Endpoint/hostname URL).

```powershell
tcp.port == 443 && ip.addr == x.x.x.x
```

![Configure Azure Front Door](/assets/img/29.png)

**Interact with C2 Beacon**: View active callbacks within the Mythic C2 framework, note the 'External IP' has been redacted for privacy.

![Configure Azure Front Door](/assets/img/30.png)

# Closing Remarks

CDN providers such as Azure Front Door, AWS CloudFront, and Cloudflare can be used as 'Cloud Domain Fronting' techniques with good effect. 

AWS has since disabled the ability and feature to use their CDN service as C2 redirector resources, due to the mass-scale abuse of such resources. 

Cloudflare's 'Trust & Safety' team monitor the public web for misuse and abuse of their cloud resources, it is difficult to predict how long this technique will work in the future.

References 

* https://medium.com/r3d-buck3t/red-teaming-in-the-cloud-installing-mythic-c2-on-azure-vm-35ef762e61b6
* https://medium.com/r3d-buck3t/red-teaming-in-cloud-leverage-azure-frontdoor-cdn-for-c2-redirectors-79dd9ca98178