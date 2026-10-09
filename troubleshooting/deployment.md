# Windows deployment troubleshooting

Use this section when Foundry Deploy refuses to continue, shows **Deployment failed**, or completes a deployment that does not start Windows. This page tells you which of its four pages to open and what to collect first.

## Start here

1. On the **Deployment failed** screen, note the step on the "Failed step: ..." line and select **View error details** to read the complete message.
2. Look at **Steps**. If the failed step is above **Prepare target disk**, the disk was not erased. If it is below, the disk was erased. If it is **Prepare target disk** itself, only "Disk partitioning failed ..." means the erase had started; with any other message, including "No drive letter is available ...", the disk is intact.
3. Open the page for your case from the table below. Each page starts with an index of its messages.
4. Collect the evidence before you turn the device off: logs kept in Windows PE memory are lost at the restart.

| What you have | Disk erased | Go to |
| --- | --- | --- |
| A password prompt, Foundry Deploy closing, or a wizard screen where **Next** or **Deploy** is unavailable | No | [Before the deployment starts](deployment/before-deployment-starts.md) |
| "Failed step: Validate answer file", "Check deployment setup", "Download Windows image" or "Check Windows image", or "Prepare target disk" with any message other than "Disk partitioning failed ..." | Depends on the media: look at **Steps**. No for "Prepare target disk" | [Checks and image download](deployment/checks-and-image-download.md) |
| "Failed step: Prepare target disk" with "Disk partitioning failed ...", or any later step, "System reboot", or a skipped step | Yes | [After the disk is erased](deployment/after-disk-erase.md) |
| "Failed step: Provision Autopilot", or a skipped Autopilot step | Yes | [Windows Autopilot troubleshooting during deployment](autopilot/during-deployment.md) |
| **Deployment complete**, then the device returns to the media or does not start Windows | Yes | [Windows does not start after deployment](deployment/device-does-not-start.md) |
| **Deployment cancelled**, or the device lost power | Look at **Steps** (after a cancellation) | [Deploy again after a failure](#deploy-again) |
| The Foundry console after the restart fails, or Windows is not activated | Yes | [After the restart troubleshooting](after-the-restart.md) |
| "Diagnostics could not be exported. Check the log for details." | Not relevant | [Logs and support information](logs-and-support.md#export-failed) |

Many messages are written by Foundry and are quoted on these pages exactly. Others come straight from a Windows tool: they start with a short summary, such as "Disk partitioning failed for disk 0.", followed by an exit code and the tool's own output in English.

## What to collect <a href="#what-to-collect" id="what-to-collect"></a>

Unless an entry says otherwise, **Collect** means this set:

- The "Failed step: ..." line and the text of **View error details** (use **Copy**).
- The archive created by **Tools > Export diagnostics...**, or `X:\Foundry\Logs\FoundryDeploy.log`.
- The device manufacturer and model, the media type (USB drive, ISO or PXE) and the Windows image and driver source you selected.

Where the files are and how to export them is in [Logs and support information](logs-and-support.md).

## Deploy again after a failure <a href="#deploy-again" id="deploy-again"></a>

Foundry Deploy cannot resume or undo a deployment, and the error screen has no retry button. Correct the cause, restart the device from the deployment media and go through the wizard again. The new deployment erases the disk again. A Windows image or a driver pack already in the USB drive's cache is verified and reused, so it is not downloaded twice.

### "Deployment stopped. The target disk may contain an incomplete installation." <a href="#cancelled" id="cancelled"></a>

The **Deployment cancelled** screen shows this sentence after every cancellation, whether **Cancel** was selected or the window was closed, and whatever the moment. The disk was erased only if **Prepare target disk** had already run: check **Steps**. Nothing is undone and the device does not restart by itself. Deploy again as described above. See [Cancel a deployment](../foundry-deploy/review-and-deploy.md#cancel-a-deployment).

### The device lost power or restarted during the deployment <a href="#power-loss" id="power-loss"></a>

If **Prepare target disk** had started, the disk holds an incomplete installation. Connect AC power, start from the deployment media and deploy again. The logs kept in Windows PE memory are lost. What can remain is the session folder under `Logs` on the USB drive's **Foundry Cache** volume and, on the target disk, `Foundry\Logs` or `Windows\Temp\Foundry\Logs`: see [Log locations](logs-and-support.md#log-locations).
