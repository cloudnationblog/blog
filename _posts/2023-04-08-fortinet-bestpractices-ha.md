---
title:  "Removing a Device from an FGT HA Configuration: Best Practices"
last_modified_at: 2023-04-08
categories: ["Fortinet"]
description: Learn the best practices for breaking an FGT HA and removing a device from the cluster without causing split breaks or disruptions to production. Follow our step-by-step guide with examples to ensure a smooth transition, from checking the primary device to reintegrating the device into HA. Get the most out of your FGT HA configuration with CloudNation Blog.
---

Breaking a FGT HA and removing a device may be necessary in certain scenarios, but it is a critical process that should be approached with caution. Care must be taken to avoid split breaks and potential disruptions to production. Although removing a device may seem straightforward, it requires careful planning and execution to ensure a smooth transition. Any mistake or oversight could lead to downtime; Therefore, it is important to follow best practices.

In some cases, it may be necessary to remove a device from an HA configuration. For example, if you upgrade the devices in an HA configuration and one of them is not upgraded, you will need to remove the non-upgraded device from the HA configuration and upgrade it manually. Another scenario where you may need to remove a device is if it has a hardware issue, and you want to remove it without causing any interruptions.

## Guidelines

1. Before removing a device from an HA configuration, it's important to ensure that the device you want to keep in production is acting as the primary device. In some cases, the device you want to remove may be acting as the primary device, so you will need to perform a failover to another device to ensure that all services are up and running after failover. Once the failover is complete, the faulty device will become the secondary device, and you can proceed with the removal process.
 
   ```shell
   Cloudnation_blog # get sys stat | grep 'Current HA mode'
   Current HA mode: Primary
   ```

2. Now that the faulty device is acting as the secondary device, you can proceed with removing it from the cluster. To do so, you should begin by removing all cables connected to other devices except for the HA cable. It's important to remove the HA cable last to prevent a split brain scenario, where both devices in the HA configuration believe they are the primary device. Once all other cables have been removed, you can safely remove the device from the HA configuration.
3. After removing the device from HA, you can proceed to make the necessary modifications to the device. Before deploying the device, certain adjustments should be made, such as configuring the Reserve Management interface, hostname, and priority (if override is enabled) under the HA configuration.
4. To reintegrate the device into HA, connect the HA cable and log in to the GUI on the primary device. Wait for the configuration to synchronize between the devices, indicated by the devices being in sync. After this, proceed with plugging in the other cables. After plugging in all the cables and ensuring that the device is configured correctly, it's important to do a failover to test that all the services are working correctly on both devices. This will ensure that there are no issues with the HA configuration and that the devices are functioning as intended.
