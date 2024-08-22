---
title: Azure Front Door as a C2 Redirector (Cloud Domain Fronting using Azure CDN)
date: 2024-08-22 12:00:00 -500
author: h3ll0clar1c3
categories: [Blog]
tags: [Red Team,C2,Azure,Front Door,CDN,Redirector] #   TAG should always be lowercase

---

Azure Front Door is a robust service for improving the availability and performance of your applications by providing global load balancing and application acceleration. 

You can use Azure Front Door to handle redirects, which is particularly useful for scenarios like URL rewriting, domain migrations, or managing different environments.

Using Azure Front Door as a redirector helps simplify complex routing rules and provides a global edge network to ensure fast and reliable redirects.

Here’s a basic guide on how to set up Azure Front Door to function as a C2 redirector:

## 1. **Create an Azure Front Door Instance**

   **Log in to the Azure Portal**: Navigate to the Azure Portal (https://portal.azure.com).

   **Create a Front Door**: Go to "Create a resource" and search for "Front Door". Click on "Front Door" and then "Create".

   **Configure Front Door**:
   - **Subscription and Resource Group**: Select your subscription and create or choose a resource group.
   - **Front Door Name**: Enter a unique name for your Front Door.
   - **Frontend Hosts**: Define the frontend hostname (e.g., `www.example.com`).

## 2. **Configure Backend Pools**

   **Add Backend Pools**: Specify the backends where the traffic will be directed. For redirection purposes, this may point to an endpoint that will handle the redirect logic or a web server.

   **Add Backends**: Enter the backend details like the hostname, and configure health probes to ensure your backend is reachable.

## 3. **Set Up Routing Rules**

   **Create Routing Rules**: Go to the "Routing rules" section and create a new rule.
   - **Name**: Enter a name for the rule.
   - **Frontend**: Choose the frontend hostname you configured earlier.
   - **Backend Pool**: Select the backend pool. This will be the target for the redirect or the service handling the redirection logic.
   - **Path Pattern**: Define the path pattern that triggers this rule (e.g., `/*` for all paths).

## 4. **Configure Redirects**

   **Configure URL Rewrite**: In the routing rules, you can set up URL rewrites to handle the redirection.
   - **Redirect Type**: Choose between `Permanent Redirect (301)` or `Temporary Redirect (302)`.
   - **Redirect Destination**: Define the target URL where requests should be redirected. This can be another domain or path.

## 5. **Custom Domain and SSL (Optional)**

   **Add Custom Domains**: If you are using a custom domain, add it under "Frontend hosts" and validate the domain ownership.

   **Configure SSL/TLS**: Set up SSL/TLS for secure connections. You can use a custom certificate or Azure Front Door’s managed certificate.

## 6. **Review and Create**

   **Review Settings**: Ensure all configurations are correct.

   **Create Front Door**: Click "Create" to deploy your Front Door instance with the redirect configurations.

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

## Carry on from here ...

Azure Front Door endpoint URL `test-dnh9h9fyezd8d5f9.a01.azurefd.net`

Add in Mythic screen grabs ...

Pics uploaded to assets folder ... 

![Configure Azure Front Door](/assets/img/10.png)

![Configure Azure Front Door](/assets/img/11.png)

![Configure Azure Front Door](/assets/img/12.png)

![Configure Azure Front Door](/assets/img/13.png)

![Configure Azure Front Door](/assets/img/14.png)

![Configure Azure Front Door](/assets/img/15.png)

![Configure Azure Front Door](/assets/img/16.png)

![Configure Azure Front Door](/assets/img/17.png)

![Configure Azure Front Door](/assets/img/18.png)

![Configure Azure Front Door](/assets/img/19.png)

![Configure Azure Front Door](/assets/img/20.png)

![Configure Azure Front Door](/assets/img/21.png)

![Configure Azure Front Door](/assets/img/22.png)

![Configure Azure Front Door](/assets/img/23.png)

![Configure Azure Front Door](/assets/img/24.png)

![Configure Azure Front Door](/assets/img/25.png)

![Configure Azure Front Door](/assets/img/26.png)

![Configure Azure Front Door](/assets/img/27.png)

![Configure Azure Front Door](/assets/img/28.png)

![Configure Azure Front Door](/assets/img/29.png)

![Configure Azure Front Door](/assets/img/30.png)

# Closing Remarks

TBC ...

References 

* https://github.com/safebuffer/vulnerable-AD/
* https://sethsec.blogspot.com/2017/08/pentest-home-lab-0x3-kerberoasting.html