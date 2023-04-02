---
title:  "Checklist of things to consider before deploying a FortiGate device in a production environment"
last_modified_date: 2023-0401 00:06:17 +0000
categories: ["Fortinet"]
description: This blog post provides a comprehensive checklist of things to consider before deploying a FortiGate device in a production environment. The post covers key topics such as device connectivity to the internet, DNS settings on Fortigate, and the ports required by Fortigate. By following this checklist, you can ensure a smooth deployment process and avoid potential issues that could impact your production environment. The blog also includes helpful screenshots and explanations to make it easier for readers to understand the concepts discussed.


---

In this blog post, we will discuss certain things that you should check before deploying a FortiGate device in a production environment. Whether you are new to the Fortinet environment or have some experience managing devices, this blog will be helpful for you.
Here is list of few things you should keep in mind.

### Device Connectivity to internet->

FortiGate needs to have internet connectivity to validate license and download signatures from Fortiguard database. You need to make sure that you have default route present in the routing table to route traffic to internet. If device is not connected to internet you may run into  issues like webfilter will stop rating websites  and affect production environment.

### DNS Settings on the Fortigate ->

The DNS settings on Fortigate play a vital role in connecting the device to Fortiguard servers. By default, FortiGate is configured to use the default Fortiguard DNS servers. However, from FortiOS 7.0.2 onwards, FortiGate will use DOT (DNS over TLS) servers only for local DNS traffic. You can validate the DNS settings on the CLI using the command '# show system DNS'. As shown in the screenshot below, selecting the default DNS settings uses the  servers which support DOT only. DOT works on port 853.

{% include lightbox.html src="Important_Fortiguard_dns.png" data="group" %}

If you want to change the DNS settings to your internal servers for any reason, make sure to change the port to 53 if your servers do not support DOT and operate on port 53

{% include lightbox.html src="Important-FGT-DNS.png" data="group" %}
 
### Ports required by Fortigate ->
If you have any upstream device make sure below mentioned ports are open .
    - 8008 
    - 8010
    - 8020 

    Here is explanation of ports.

    Port 8008 is used by FortiGate to authenticate with FortiGuard when an HTTP override request occurs for FortiGuard web filter HTTP override authentication. Port 8010 is used for HTTPS override requests for FortiGuard web filter HTTPS override authentication. Port 8020 is used for FortiGuard web filter warning authentication.

