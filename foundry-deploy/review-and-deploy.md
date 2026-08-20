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

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-deploy-review-01-summary.png`
- **Capture:** Show the deployment summary with sanitized device and tenant information.
{% endhint %}

## Start deployment

Start only when every value is correct. Foundry opens **Confirm disk erase** before crossing the destructive boundary. Verify the disk number, model, bus, size, and selected operating system before accepting.

{% hint style="danger" %}
Accepting the confirmation allows Foundry to clean and repartition the selected disk. Existing data on that disk will be lost.
{% endhint %}

Do not power off the device, disconnect required networking, or remove deployment media while Foundry is working.

The progress page reports the current step, completed-step count, and overall progress when the operation can be measured.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-deploy-progress-01-running.png`
- **Capture:** Show a deployment in progress with current step and overall progress.
{% endhint %}
