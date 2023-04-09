---
title:  "Configuring Reserve HA Management: How to Access a Secondary Device"
last_modified_at: 2023-04-09
categories: ["Fortinet"]
description: Learn how to access a secondary device in an HA configuration by configuring reserve HA management on both devices. Follow our step-by-step guide with examples to gain access to the secondary device through the GUI

header:
   teaser: Secondary-dev-HA.png


---

The blog post discusses how to access a secondary device in a High Availability (HA) configuration. Typically, in an HA configuration, only the primary device can be accessed via the GUI. However, there may be situations where it's necessary to access the secondary device through the GUI. The post explains the configuration steps required to access the secondary device by configuring reserve HA management on both devices. It provides an example of the HA configuration on the primary and secondary devices, along with the configuration of port3 on both devices. Once reserve HA management is configured, the secondary device can be accessed using HA.In the example below, we will explore how to access a secondary device in an HA configuration.

### Demonstration

{% include lightbox.html src="Secondary-dev-HA.png" data="group" %}
 
After configuring a device in HA, if you attempt to access it using the IP address 192.168.1.1, you will only be able to access the primary device. This is because the primary device will synchronize its configuration and become the only firewall that can be accessed through the GUI.

To get the access of the Secondary device we need to configure reserve HA management on the Both devices.

```
HA configuration on Primary ->
Cloudnation_blog # show  system  ha
config system ha
    set group-name "Cloudnation.blog"
    set mode a-p
    set hbdev "port10" 0 
    set ha-mgmt-status enable
    config ha-mgmt-interfaces
        edit 1
            set interface "port3"
            set gateway 10.10.10.254
        next
    end
    set override disable
end
```
```

Cloudnation_blog # show  system  interface port3
config system interface
    edit "port3"
        set ip 10.10.10.1 255.255.255.0
        set type physical
        set snmp-index 3
    next
end
```


## Secondary Firewall

 ```
 Cloudnation_blog # show  system  interface port3
 config system interface
    edit "port3"
        set ip 10.10.10.2 255.255.255.0
        set type physical
        set snmp-index 3
    next
 end

 HA config on Secondary

 Cloudnation_blog2 # show  system  ha
 config system ha
    set group-name "Cloudnation.blog"
    set mode a-p
    set hbdev "port10" 0 
    set ha-mgmt-status enable
    config ha-mgmt-interfaces
        edit 1
            set interface "port3"
            set gateway 10.10.10.254
        next
    end
    set override disable
 end
 ```

Once HA reserve management is configured you should be able to access Secondary device using IP 10.10.10.2 and primary on 10.10.10.1.


