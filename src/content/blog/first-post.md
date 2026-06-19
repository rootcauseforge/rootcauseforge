---
title: 'Forcing to Reload the Big-IP configuration'
description: 'This article is useful for forcing Big-IPs to reload their configuration from binary files. It can be helpful when you have the correct configurations but, for some reason, they have stopped working as expected due to corruption; it also applies when monitors cannot maintain their state or configuration sync fails, and all possible communication and configuration issues have already been ruled out.'
pubDate: 'August 2025'
heroImage: ''
---

#### Symptom:

The settings are correct, but for some reason they stopped working as expected due to corruption. This also applies when monitors cannot maintain their state or the Sync configuration fails, and all possible communication and configuration issues have already been ruled out.

In my case, my Big-IP A was active with monitors showing red. This started happening unexpectedly; however, I had no communication issues with the members, and all my configurations were correct, so I decided to run this process. To avoid any disruption—since the process requires a reboot—I temporarily put my Big-IP A into Stand -By mode so I could run the process in this state (Stand-by). Additionally, this confirmed that Big-IP B—despite having exactly the same configurations (due to DSC sync)—did not exhibit the monitoring issue. This confirmed that it was not a configuration or communication problem, but rather that something had become corrupted on my primary Big-IP (A). 

#### Environment

Big-IP

#### Diagnostic

Corruption

#### Procedure

**GUI**

<span style="color: red;">
Put the affected device into Stand-By mode to prevent disruption, since the process involves rebooting the device with the problem. 
</span>
If you don’t know how to perform a failover, follow the process described below under “Failover.” In my case, I put Big-IP A into Stand-By mode because that was the device with the problem.


**SSH (Big-IP A)**

1. Connect to the device via SSH.
2. Enter the command: 

*tmsh*

3. Save the configuration with the following command: 

*save sys config*

4. Enter the following command:

*load sys config verify*

Note: At this point, you can correct any configuration errors; warnings can be ignored.

**GUI (Big-IP A)**

5. Log in to the device in Standby mode via the web interface.
6. Go to the menu: Device Management > Devices
7. Select the device labeled (Self).
8. A new screen will appear; at the bottom, click the Force Offline button.


**SSH (Big-IP A)**

9. Return to the SSH connection and enter the following command (outside of tmsh; if you are still in tmsh, you can run the following command to switch to bash 

*bash*

to exit and run the following command): 

*touch /service/mcpd/forceload*

10. Reboot the device using the following command: 

*reboot*

**GUI (Big-IP A)**

11. Once the reboot is complete, log back into the same device (Big-IP A) via the web interface.
12. Go to the menu: Device Management > Devices
13. Select the device labeled (Self).
14. Click the Release Offline button.

Synchronizing Big-IP Devices in HA
Note: In my case, I did this because Device B has always worked correctly (even with the same configurations).

**GUI (Big-IP B)**

1. Go to the Device Management > Overview menu via the web interface.
2. Select the currently active device (Big-IP B) to synchronize its configuration with the group.
3. Click the Sync button.

Failover to return Big-IP A to its original (Active) state

**GUI (Big-IP B)**

1. Log in via the graphical interface to the currently active device (Big-IP B).
2. Go to the menu: Device Management > Devices
3. Select the device labeled (Self)
4. A new screen will appear; at the bottom, click the Force to Standby button.


#### Validation

Once the previous process has been completed, Device A is back to its original state, active and with its configurations loaded from binaries.

Verify whether the unexpected behavior has been resolved.