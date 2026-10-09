# Select the target

On **Target device**, the first step of the wizard, you choose the disk that receives Windows, set the computer name and check that Foundry Deploy detected the right hardware.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-deploy-target-01-disk-selection.png`
- **Capture:** Show the **Target device** step of a release build with **Deployment settings** (**Answer file**, **Computer name**, **Target disk**, the disk erase notice), **Device inventory** and the **Apply firmware updates** check box. Use a demonstration computer name and hide the serial number.
{% endhint %}

## Choose the target

1. In **Answer file**, keep **Use Foundry settings** or select a custom answer file. The field appears only when the media carries custom answer files.
2. In **Computer name**, check or type the name. The field is read-only when the administrator locked the generated name or when you selected a custom answer file.
3. In **Target disk**, select the internal disk that receives Windows. Compare the model, size and bus with the physical device.
4. Leave **Apply firmware updates** checked unless you were told otherwise.
5. Under **Device inventory**, check that the manufacturer, model and serial number are those of the device in front of you.
6. Select **Next**.

{% hint style="danger" %}
All data on the selected disk is lost when the deployment starts. Size alone does not identify a disk: check its model too.
{% endhint %}

## What each field means

| Field | What it means |
| --- | --- |
| **Answer file** | With a custom file, the screen shows "Windows uses the selected answer file. Foundry keeps your settings and adds its post-installation command when the deployment needs one." The file then sets the computer name and the Windows setup options. See [what a custom answer file overrides](../foundry-osd/customization/unattend.md#what-a-custom-answer-file-overrides). |
| **Computer name** | 1 to 15 letters, digits or hyphens, and not digits only. The administrator can pre-fill it from the serial number or other device data: see [Machine naming](../foundry-osd/customization/machine-naming.md). |
| **Target disk** | One line per internal disk: number, model, size, bus. Disks connected over USB are not listed. A line ending in **Blocked: system disk**, **Blocked: boot disk**, **Blocked: read-only** or **Blocked: offline** cannot be used. |
| **Apply firmware updates** | Foundry looks for a system firmware update for this device in Microsoft Update Catalog and adds it to the installed Windows, which applies it after the restart. Checked by default. On a virtual machine it is cleared and marked **Not available on virtual machines**. |

**Device inventory** is read-only. **Firmware status** shows **Detected** when the device reports the identifier that a firmware update search needs, and **Unavailable** otherwise.

## What happens to the disk

Foundry Deploy always prepares the disk the same way. There is no option to keep existing partitions and no support for legacy BIOS (CSM) boot: the device firmware must start in UEFI mode.

| Partition | Size |
| --- | --- |
| EFI system partition | 260 MB |
| Microsoft reserved (MSR) | 16 MB |
| Recovery | 5,120 MB |
| Windows | The rest of the disk |

The disk is converted to GPT and Windows is set up for UEFI boot. Just before erasing, Foundry checks that the disk is still the one you confirmed. If it was removed, replaced or can no longer be told apart from another disk, the deployment stops and nothing is erased.

## If something stops you

- The disk list is empty, or the disk you need is blocked.
- An error message appears under **Answer file** or **Computer name**.
- **Next** stays unavailable.

Each case has an entry in [Windows deployment troubleshooting](../troubleshooting/deployment.md).

Next: [Select Windows](operating-system.md).
