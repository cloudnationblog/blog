---
title:  "A Step-by-Step Guide to Configuring FortiGate Firewall for Local FortiGuard Traffic Control with SDWAN"
last_modified_at: 2023-04-02
categories: ["Fortinet"]
description: Learn how to control local FortiGuard traffic on your FortiGate firewall when using SDWAN. Discover how to configure your FortiGate to send Fortinet-FortiGuard traffic through a specific interface, such as a fiber connection, and prevent it from using an expensive LTE interface except when necessary. With step-by-step instructions and verification tips, this blog post helps you optimize your FortiGate configuration and save money.



---

### Introduction

In this blog, we'll explore how you can control local FortiGuard traffic when SDWAN is configured on your FortiGate firewall. Specifically, we'll look at how you can configure your FortiGate to ensure that certain types of traffic always go through a specific interface.
For example, suppose you have two WAN interfaces, one of which is an LTE interface that you don't want to utilize for Fortiguard traffic. You only want to send traffic through the LTE interface when the other interface goes down. We'll show you how to set up your FortiGate to achieve this goal.

In the below example, we have two WAN interface port1 and port2. Port1 is Fiber connection and Port2 is LTE connected which we just to use as backup only when port1 goes down.

{% include lightbox.html src="interface-local-2.png" data="group" %}

### SDWAN Configuration

We have configured port1 and port2 as member of SDWAN. 

### Performance SLA

You can configure performance SLAs for both WAN interfaces to ensure that all traffic is automatically rerouted in the event of an interface failure. For example, if port1 goes down, the performance SLA will trigger failover and then only the LTE interface will be used for FortiGuard traffic.

 {% include lightbox.html src="interface-local-1.png" data="group" %}


### SDWAN Rule 

To control local FortiGuard traffic when SDWAN is configured, you can configure a manual rule in your FortiGate firewall's SDWAN policy to force Fortinet-FortiGuard traffic through port1, preventing the device from sending FortiGuard traffic through port2.

 {% include lightbox.html src="interface-local-3.png" data="group" %}
 

### Fortiguard settings 

In addition to configuring a manual SDWAN rule and performance SLAs for Fortinet-FortiGuard traffic, you will also need to ensure that the FortiGuard settings are properly configured in your FortiGate firewall.

Specifically, you will need to make sure that the interface selection method is set to SDWAN, which will force the firewall to use the SDWAN rule that you created for Fortinet-FortiGuard traffic. This will ensure that all Fortinet-FortiGuard traffic is always routed through port1.

```
Config system fortiguard
    set update-server-location usa
    set interface-select-method sdwan >>>>>>>>>
end
```
### Verification

To ensure that the rule is working properly, you can verify the hit count, which will confirm that the SDWAN rule is correctly routing FortiGuard traffic through the desired interface (in this case, port1) and preventing traffic from going through the other interface (in this case, port2). This can help you save money by ensuring that traffic is not unnecessarily sent through an expensive LTE interface.

 {% include lightbox.html src="interface-local-4.png" data="group" %}
 

 
