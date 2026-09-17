# Review and deploy

The Summary step is the final opportunity to validate the deployment choices.

## Review the summary

Confirm:

- Target disk and computer name.
- Windows release, language, edition, and license channel.
- Driver pack.
- Firmware options.
- Windows Autopilot method and, for zero-touch hardware hash upload, the configured group tag.
- Optional features and other deployment customization.

<figure>
  <img src="../.gitbook/assets/foundry-deploy-review-01-summary.png" alt="Foundry Deploy deployment summary before confirmation">
  <figcaption>Review every deployment choice before starting the destructive operation.</figcaption>
</figure>

When the media includes [custom answer files](../foundry-osd/customization/unattend.md), also review the selected **Answer file**. A custom file controls the computer name and OOBE settings; Foundry does not apply its native values for these settings.

## Start deployment

Start only when every value is correct. Foundry opens **Confirm disk erase** before crossing the destructive boundary. Verify the disk number, model, bus, size, and selected operating system before accepting.

{% hint style="danger" %}
Accepting the confirmation allows Foundry to clean and repartition the selected disk. Existing data on that disk will be lost.
{% endhint %}

Do not power off the device, disconnect required networking, or remove deployment media while Foundry is working.

## Checks before disk preparation

Foundry checks the selected image settings, storage location and known space requirements before preparing the target disk.

- **USB with enough usable cache space:** Foundry downloads or reuses the Windows image, checks its contents against the catalog hash when one is supplied, and verifies the selected edition before erasing the target. The prepared image is then used for deployment. Cache verification and downloads display their own progress.
- **ISO, or USB when the image needs target-disk storage:** Foundry checks source access and the space requirements it can determine first. The complete download, file verification and edition check finish after the target disk is prepared. The progress page explains when image validation needs target storage.

An error during the checks before disk preparation stops deployment before the target is erased. Correct the reported image, network or storage issue before retrying. See [deployment troubleshooting](../troubleshooting/deployment.md#checks-before-disk-preparation-fail).

{% hint style="warning" %}
These checks cannot guarantee that every later step will succeed. Network access can change, and additional space may be needed for extracted drivers, optional features and updates. When the image needs target-disk storage, an image failure can still occur after erasure. Keep a recovery option available.
{% endhint %}

## Follow progress

The progress page reports the current step, completed-step count, and overall progress when the operation can be measured.

<figure>
  <img src="../.gitbook/assets/foundry-deploy-progress-01-running.png" alt="Foundry Deploy showing the current deployment step and overall progress">
  <figcaption>Follow the current step and overall deployment progress.</figcaption>
</figure>

## Cancel deployment

Select **Cancel** at the bottom right of the progress page. Foundry acknowledges the request and stops at a safe boundary. Downloads and cache checks can stop promptly; disk preparation, Windows servicing, and other changes already in progress may need to finish first. Keep the device powered on and the deployment media connected until Foundry reports that deployment was cancelled.

Cancellation does not undo completed changes. If disk preparation has started, the target may contain an incomplete installation and may not boot. Collect the logs, then restart the workflow when ready; there is no resume operation. A cancelled deployment does not automatically reboot.
