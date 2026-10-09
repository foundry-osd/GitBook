# Foundry Connect

Foundry Connect opens after the startup console on a target device started from deployment media. It gets the device online in Windows PE and holds the startup until Internet access is confirmed, because the next stage needs GitHub to prepare Foundry Deploy.

Most of the time you do nothing. With a cable and DHCP, or with a network profile that the administrator put on the media, the header changes from **Waiting for network** to **Network ready** by itself.

## Continue to Foundry Deploy

**Network ready** means that the device received an answer from the Internet. It does not prove that every site needed later is reachable. See [Network readiness](network-readiness.md) for the exact test.

When **Network ready** appears, Foundry Connect shows **Continuing automatically in 10s** and counts down, then closes and the startup continues. Select **Continue** to skip the wait. If the connection drops during the countdown, the countdown is cancelled and starts again at the next **Network ready**.

{% hint style="warning" %}
Do not close Foundry Connect to get past it. Closing the window cancels the startup: Foundry Deploy does not open. See ["Boot was cancelled. Deployment will not continue."](../troubleshooting/windows-pe-startup.md#boot-cancelled) to start again.
{% endhint %}

## Menus

| Menu | Use it to |
| --- | --- |
| **Theme**, **Language** | Change the appearance or the display language of Foundry Connect. |
| **Tools > Refresh status** | Check the network now instead of waiting for the next automatic check. Foundry Connect checks every 10 seconds. |
| **Tools > Export diagnostics...** | Save the logs to the USB drive for support. See [Export logs from Foundry Connect and Foundry Deploy](../troubleshooting/logs-and-support.md#export-logs-from-foundry-connect-and-foundry-deploy). |
| **Tools > Export raw diagnostics...** | Save unfiltered logs, which can contain credentials and network names. Use it only when a support contact you trust asks for it. |

The version of Foundry Connect is shown at the top right of the window.

## Where to go next

| I want to | Go to |
| --- | --- |
| Understand the console that appears before Foundry Connect | [Windows PE startup](windows-pe-startup.md) |
| Connect to Wi-Fi, or read a status on screen | [Network readiness](network-readiness.md) |
| Fix a device that stays on **Waiting for network** | [Network and Foundry Connect troubleshooting](../troubleshooting/network.md) |
| Continue after **Network ready** | [Foundry Deploy](../foundry-deploy/README.md) |
