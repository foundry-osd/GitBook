# Post-installation

Use **Customization > Post-installation** to run your own scripts, commands and installers on the target device after Windows is installed and before the first sign-in. Your actions run in the order of the list, after Foundry's own setup tasks.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-post-installation-01-ordered-actions.png" alt="Foundry OSD Post-installation page with five enabled actions in order: two software installations, a restart, a PowerShell script and a command line">
  <figcaption>Actions run from top to bottom. Content status shows whether the imported files are still in the local library.</figcaption>
</figure>

## Before you start

- Every script and installer must run to the end without a prompt. Actions run as SYSTEM, the Windows built-in account with full rights on the device, with no signed-in user, no window and no keyboard input.
- Actions that use imported files need the complete ISO or USB drive during deployment. A PXE boot image alone does not carry them: see [what each media type carries](../media/README.md#what-each-media-type-carries).
- Use content built for the Windows architecture you deploy, x64 or ARM64.

## Configure actions

1. Open **Customization > Post-installation** and turn on the switch in the page header.
2. On the toolbar above the table, select **Add action**, then **PowerShell script (.ps1)**, **Command line**, **Software (.exe/.msi)** or **Restart Windows**.
3. Enter a **Name**. The technician sees it in the console after the restart.
4. Under **Package content**, select **Import file** or **Import folder**. Content is optional for **Command line** and not used by **Restart Windows**.
5. Fill in the fields of the action type, then read **Command preview**: it is the command Foundry will run. `{ContentRoot}` and `{LogRoot}` appear in the preview only; they are not replaced if you type them in your arguments.
6. Review **Execution settings**, defined in [Execution settings](post-installation/how-actions-run.md#execution-settings): the timeout is 1800 seconds by default. Then select **Save**.
7. Select a row, then use **Move up** and **Move down** on the toolbar to set the order. **Edit action**, **Disable** or **Enable**, and **Remove** also act on the selected row.

| Action type | Fields |
| --- | --- |
| **PowerShell script (.ps1)** | **Script or installer path (relative to content)**, **PowerShell arguments**, **Script arguments** |
| **Command line** | **Command line**, one line with its arguments |
| **Software (.exe/.msi)** | **Script or installer path (relative to content)**, **Arguments or MSI properties**, **Generate installation log** (MSI only) |
| **Restart Windows** | **Restart delay (seconds)**, 0 to 86400, default 0 |

Foundry adds nothing to your command: enter silent and no-restart switches yourself, for example `/qn /norestart` for an MSI.

{% hint style="warning" %}
Foundry waits for every process an action starts, not only the first one. An updater, a tray application or the installed application left running holds the action until its timeout, and the sequence then stops.
{% endhint %}

What Foundry runs for each type, and what it does with each exit code, is in [How actions run](post-installation/how-actions-run.md).

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-post-installation-02-action-dialog.png`
- **Capture:** Show the **Add action** dialog for a **Software (.exe/.msi)** action with an imported MSI: name, installer type, package content, installer path, arguments, **Generate installation log**, the execution settings and the command preview.
{% endhint %}

## Prepare content

- Import a folder when the script or installer needs other files, such as a transform or a CAB file. Relative paths are kept.
- Leave **Working directory (relative; empty uses content root)** empty to run from the root of the imported content, or enter a folder inside it. Without content, a command runs from a temporary folder.
- Foundry copies the content to its library in `%LOCALAPPDATA%\Foundry\Packages\PreOobe`. To pick up a new version of your source, select **Edit action** and import again; **Refresh** only rechecks the library copy.
- An import is refused beyond certain path lengths and counts: see [Import limits](post-installation/how-actions-run.md#import-limits).

## Restarts and secrets

Never let a script or an installer restart Windows itself: the action is then marked interrupted and the sequence stops. Add a **Restart Windows** action where the restart must happen, or declare the restart code of the installer. See [Restarts](post-installation/how-actions-run.md#restarts).

{% hint style="warning" %}
Do not pass passwords, keys or tokens as arguments. The full command line of every action is saved in `C:\Windows\Temp\Foundry\State\PreOobe\plan.json` and its output in `C:\Windows\Temp\Foundry\Logs\PreOobe`. Both stay on the device after deployment.
{% endhint %}

## Check the result

- On the page, no warning bar is shown and every enabled action has **Available** or **Not required** under **Content status**.
- On a test device, after the restart, the **Foundry Post-installation** console lists Foundry's setup tasks, then your actions under the names you gave them, then `Cleanup`. Every action shows `[Succeeded]` and the console reports `Post-installation completed.` Script output is not shown: [After the restart](../../foundry-deploy/after-the-restart.md) explains each line and the restarts.

## If something goes wrong

| What you see in Foundry OSD | What to do |
| --- | --- |
| "Content import failed. Check access, available space, file names and path lengths." | One message for every [import limit](post-installation/how-actions-run.md#import-limits). Shorten paths, copy the content to a plain local folder or free space, then import again. The Foundry OSD log names the limit, for example `PreOobe.InvalidPackagePath`. |
| "Import the missing content for "\<name\>"." | Select **Edit action**, import the same file or folder, then **Save**. |
| "The configuration changed while you were editing. Open the action again." | The configuration was replaced while the dialog was open, for example by a sync. Reopen the action. |
| "Final media creation failed. Custom Windows image media preparation failed." with a code that starts with `PreOobe.` | The failure comes from Post-installation content, even when no custom image is configured. See [Content cannot be copied to the media](../../troubleshooting/media-creation/usb-drive-and-device-start.md#media-content). |

Failures on the target device are in [After the restart troubleshooting](../../troubleshooting/after-the-restart.md).

## Related

- [How actions run](post-installation/how-actions-run.md)
- [After the restart](../../foundry-deploy/after-the-restart.md)
- [After the restart troubleshooting](../../troubleshooting/after-the-restart.md)
- [Unattend (custom answer files)](unattend.md)
