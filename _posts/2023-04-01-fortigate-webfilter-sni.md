---
title:  "Fortigate Web Filter SNI"
last_modified_date: 2023-0401 00:06:17 +0000
categories: ["Fortinet"]
description: Learn how FortiOS Webfilter categorizes websites based on their content using SNI, a TLS protocol extension. Discover the process of how Fortigate extracts the hostname and queries the Fortiguard servers to categorize websites. Find out how to verify the category of a website and explore the importance of SNI in securely hosting multiple websites or services on a single IP address.
header:
    teaser: /images/blog/SNI-client-hello.PNG

---

FortiOS Webfilter is a powerful tool used by network administrators to control and monitor web traffic on their networks. One of its key features is its ability to categorize websites based on their content. In this blog post, we'll take a closer look at how FortiOS Webfilter makes these categorization decisions.

FortiOS will extract the hostname from the SNI extension in the "Client Hello" message of the TLS handshake and use the hostname for category query. 
Before we discuss how Webfilter works in detail let’s discuss the importance of SNI.

### What is SNI 

During the initial handshake process, SNI (Server Name Indication) enables a client to indicate the hostname of the server it wants to communicate with. SNI is an extension to the TLS (Transport Layer Security) protocol.

Practically, SNI enables a server to host multiple websites or services on a single IP address, each with its own unique hostname. In the absence of SNI, it is difficult to host multiple websites or services securely on the same server because a server can only present one SSL/TLS certificate for one IP address.

###  Exploring Fortigate Web Filtering Process using SNI

Now, We will discuss the process in details using packet capture. In the below example user (172.20.1.2) is trying to open website example.net and we will discuss in details how fortigate will decide the category of website

1. When end user will try to open website using browser . FortiGate will extract server name from SNI flag  and query category from the Fortiguard servers.
{% include lightbox.html src="SNI-client-hello.PNG" data="group" %}
2. Fortigate will send queries to Fortiguard servers for domain “example.net” and will categorize website by getting response from servers. To verify ,We can check the category of website on the Fortiguard website directly using link -> (https://www.fortiguard.com/webfilter)
{% include lightbox.html src="SNI-Fortiguard.PNG" data="group" %}
3. To check the category of website on the FortiGate we can validate cache. You can use the command below to validate the results.
    ```shell
    dia webfilter fortiguard cache dump
    34000000|34000000 27.20425     1    0 00000001 P Dhttps://example.net/ 
    ```

In the above example, the domain `example.net` domain is in category (Hex) 34

When we convert Hexadecimal to Decimal, it will be `52 <----- Information Technology`.

To know the category ID in the FortiGate, use below command:

```shell
get webfilter categories 
```