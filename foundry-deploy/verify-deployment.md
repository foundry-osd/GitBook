# Verify deployment

A deployment ends on one of three screens: **Deployment complete**, **Deployment failed** or **Deployment cancelled**. This page tells you what to do on each one before the device restarts.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-deploy-verify-01-success.png`
- **Capture:** Show the **Deployment complete** screen of a release build with the restart countdown ("This computer will restart in ...") and the **Steps** list. Use a demonstration computer name and hide the IP and MAC addresses.
{% endhint %}

## Deployment complete

1. Look through **Steps** for skipped steps and point at each one to read its reason. A skipped **Download driver pack** or **Configure Windows recovery** changes what the device has after the restart: see [Deployment steps](review-and-deploy.md#deployment-steps).
2. Remove the deployment media.
3. Let the device restart, or select **Reboot**.

| What the screen says | What happens |
| --- | --- |
| "This computer will restart in \<n>s." | The device restarts by itself when the countdown ends. **Reboot** restarts it at once. |
| "Remove the boot media, then select Reboot." | The administrator turned automatic restart off. Nothing happens until you select **Reboot**. |

Automatic restart is on by default with a delay of 10 seconds. With a delay of 0 seconds the device restarts as soon as the deployment completes, and you cannot read the screen. The administrator changes both settings on the [General](../foundry-osd/general.md) page, and the **Completion** category of the summary shows them before you start.

A completed deployment means Windows is on the disk and the work planned for the first start is in place. It does not mean that work has run. Continue with [After the restart](after-the-restart.md).

## Deployment failed

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-deploy-verify-02-error.png`
- **Capture:** Show the **Deployment failed** screen of a release build with the "Failed step: ..." line, the **View error details** button and the failed step marked in the **Steps** list. Hide the IP and MAC addresses.
{% endhint %}

The device does not restart after a failure. Do this before you turn it off:

1. Note the step named on the "Failed step: ..." line.
2. Select **View error details**. The **Error details** window shows the complete message.
3. Select **Copy** to copy the message, or **Open log file** to read the deployment log.
4. In the menu bar, select **Tools > Export diagnostics...** to save the logs to the USB drive. See [Export logs from Foundry Connect and Foundry Deploy](../troubleshooting/logs-and-support.md#export-logs-from-foundry-connect-and-foundry-deploy).
5. Find the failed step in [Windows deployment troubleshooting](../troubleshooting/deployment.md).

Check where **Prepare target disk** sits in **Steps**. If the failed step is above it, the disk was not erased. If it is below, the disk was erased and the device may not start.

Foundry Deploy cannot resume or roll back a failed deployment, and the error screen has no retry button. After correcting the cause, restart the device from the deployment media and go through the wizard again: this erases the disk again. Images and driver packs already downloaded to the USB drive's cache are verified and reused.

## Deployment cancelled

The screen shows "Deployment stopped. The target disk may contain an incomplete installation." There is no restart and no error detail. Export the logs if you need them, then restart the device from the deployment media to start again. See [Cancel a deployment](review-and-deploy.md#cancel-a-deployment).

## The restart does not start

If the restart command fails, the success screen turns into **Deployment failed** with "Failed step: System reboot". Windows is installed. Export the logs, remove the deployment media and turn the device off and on.
