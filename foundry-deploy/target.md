# Select the target

The Target step identifies the device and selects where Windows will be installed.

## Review the device

Confirm the displayed hardware information matches the physical device. Review firmware, Windows Autopilot, and other options enabled by the media configuration.

## Set the computer name

Enter a name from 1 to 15 characters using letters, numbers, or hyphens. Follow the organization’s naming policy and avoid a name already assigned to another device.

## Select the disk

Choose the intended internal target disk. Foundry blocks disks that do not meet deployment eligibility rules.

<figure>
  <img src="../.gitbook/assets/foundry-deploy-target-01-disk-selection.png" alt="Foundry Deploy target disk selection">
  <figcaption>Verify the device and select the intended deployment disk.</figcaption>
</figure>

{% hint style="danger" %}
All data on the selected target disk will be lost. Capacity alone is not enough to identify a disk; verify its model and position when available.
{% endhint %}
