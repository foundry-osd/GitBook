# Select the target

The Target step identifies the device and selects where Windows will be installed.

## Review the device

Confirm the displayed hardware information matches the physical device. Review firmware, Windows Autopilot, and other options enabled by the media configuration.

## Select a custom answer file (unreleased)

{% hint style="info" %}
Custom answer-file selection is part of [Foundry PR #314](https://github.com/foundry-osd/foundry/pull/314) and is not available in release `v26.9.1.1`.
{% endhint %}

When the media enables custom answer files, choose one before the computer-name field or select **Use Foundry settings**. A custom file controls the computer name, time zone, and OOBE, so Foundry's native naming and OOBE settings are suppressed even when the file omits those values.

Review the selected file in the summary and confirmation. Missing files, an invalid default, architecture mismatches, and known Autopilot conflicts can block deployment. Foundry does not silently switch to native settings. See [custom answer-file preparation and compatibility](../foundry-osd/customization/unattend.md).

## Set the computer name

The following naming rules apply when using native Foundry settings.

Enter a name from 1 to 15 characters using letters, numbers, or hyphens. Follow the organization’s naming policy and avoid a name already assigned to another device.

The policy authored in Foundry OSD can request a complete manual name or compose one from fixed text, device data, and random text. A composed name can be locked or made editable as a complete value. A name that meets Windows character rules can still be rejected when it does not satisfy the configured component policy.

Foundry Deploy resolves serial number, manufacturer, model, asset tag, and system UUID from the current device. Deployment is blocked when a required value is unavailable or contains a known firmware placeholder.

## Select the disk

Choose the intended internal target disk. Foundry excludes disks connected over USB and blocks system, boot, read-only, and offline disks.

<figure>
  <img src="../.gitbook/assets/foundry-deploy-target-01-disk-selection.png" alt="Foundry Deploy target disk selection">
  <figcaption>Verify the device and select the intended deployment disk.</figcaption>
</figure>

{% hint style="danger" %}
All data on the selected target disk will be lost. Capacity alone is not enough to identify a disk; verify its model and position when available.
{% endhint %}
