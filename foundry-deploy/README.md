# Foundry Deploy

Foundry Deploy is the wizard that installs Windows on the target device. It opens in Windows PE once [Foundry Connect](../foundry-connect/README.md) reports that the network is ready.

{% hint style="danger" %}
Deployment erases the disk you select. Nothing is erased while you are in the wizard: the erase starts only after you select **Deploy** and accept **Confirm disk erase**.
{% endhint %}

## Before you start

- Connect the device to AC power and keep it connected.
- Disconnect storage that must not be erased.
- Have the Deployment password ready when the administrator protected the media.
- Keep the deployment media connected until Foundry Deploy shows **Deployment complete**.

## Open Foundry Deploy

1. If a **Protected deployment** window asks "Enter the technician password to continue.", type the Deployment password and select **Continue**. Media created without Password protection skips this window.
2. Wait on the **Welcome** screen while it shows **Initializing components...**. Foundry Deploy detects the hardware, lists the disks and loads the Windows and driver catalogs.
3. Select **Start deployment**.

A wrong password shows "The password is incorrect. Try again.". Each failed attempt adds one second of waiting before the next prompt, up to five seconds. **Cancel** closes Foundry Deploy. A lost password cannot be recovered: the administrator must recreate the media, as explained in [Password protection](../foundry-osd/general.md#password-protection).

## Wizard steps

The wizard shows its steps across the top. Use **Next** and **Previous** to move between them.

| Step | What you do | Shown |
| --- | --- | --- |
| **Target device** | [Select the target](target.md): disk, computer name, answer file, firmware updates | Always |
| **Operating system** | [Select Windows](operating-system.md) | Always |
| **Drivers** | [Select a driver pack](driver-pack.md) | Always |
| **Autopilot** | [Windows Autopilot step](autopilot.md) | Only when the media uses a JSON profile or zero-touch hardware hash upload |
| **Domain join** | [Domain Join step](domain-join.md) | Only when there is a join account to enter, or a domain or an OU to choose |
| **Summary** | [Review and deploy](review-and-deploy.md) | Always |

After you start the deployment, continue with [Verify deployment](verify-deployment.md), then [After the restart](after-the-restart.md).

## Menu bar

The menu bar stays available on every screen, including the error screen.

| Menu | Use it to |
| --- | --- |
| **Theme** | Switch between **System**, **Light** and **Dark** |
| **Language** | Change the display language of Foundry Deploy |
| **Tools** | Open or export the logs: **Open log file**, **Export diagnostics...**, **Export raw diagnostics...** |
| **About** | Read the Foundry Deploy version |

Use **Tools** before you restart a device whose deployment failed. See [Export logs from Foundry Connect and Foundry Deploy](../troubleshooting/logs-and-support.md#export-logs-from-foundry-connect-and-foundry-deploy).

A notice next to the version number, "New version available. Update Foundry OSD and rebuild boot media." or "Update Foundry OSD and rebuild boot media.", means the media was created with an older Foundry OSD. You can still deploy. Tell the administrator, who updates Foundry OSD and then [recreates or updates the media](../foundry-osd/media/README.md).

## If something stops you

| What you see | Go to |
| --- | --- |
| The password is refused, or Foundry Deploy closes as soon as it opens | [Before the deployment starts](../troubleshooting/deployment/before-deployment-starts.md) |
| **Next** stays unavailable on **Target device** | [Before the deployment starts](../troubleshooting/deployment/before-deployment-starts.md) |
| **Deployment failed** | [Verify deployment](verify-deployment.md), then [Windows deployment troubleshooting](../troubleshooting/deployment.md) |
