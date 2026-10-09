# Post-installation

Use **Customization > Post-installation** to run your own scripts, commands and installers on the target device after Windows is installed and before the first sign-in. Your actions run in the order of the list, after Foundry's own setup tasks.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-post-installation-01-ordered-actions.png" alt="Foundry OSD Post-installation page with five enabled actions in order: two software installations, a restart, a PowerShell script and a command line">
  <figcaption>Actions run from top to bottom. Content status shows whether the imported files are still in the local library.</figcaption>
</figure>

## Before you start

- Every script and installer must run to the end without a prompt. Actions run as SYSTEM, with no signed-in user, no window and no keyboard input.
- Actions that use imported files need the complete ISO or USB drive during deployment. A PXE boot image alone does not carry them: see [what each media type carries](../media/README.md#what-each-media-type-carries).
- Use content built for the Windows architecture you deploy, x64 or ARM64.

## Configure actions

1. Open **Customization > Post-installation** and turn on the switch in the page header.
2. Select **Add action**, then **PowerShell script (.ps1)**, **Command line**, **Software (.exe/.msi)** or **Restart Windows**.
3. Enter a **Name**. The technician sees it in the console after the restart.
4. Under **Package content**, select **Import file** or **Import folder**. Content is optional for **Command line** and not used by **Restart Windows**.
5. Fill in the fields of the action type, then read **Command preview**: it is the command Foundry will run.
6. Review **Execution settings** and select **Save**.
7. Select a row, then use **Move up** and **Move down** to set the order. **Edit action**, **Disable** or **Enable**, and **Remove** also act on the selected row.

| Action type | What Foundry runs | Fields |
| --- | --- | --- |
| **PowerShell script (.ps1)** | `powershell.exe` (Windows PowerShell 5.1), your **PowerShell arguments**, `-File "<script>"`, then your **Script arguments** | **Script or installer path (relative to content)**, **PowerShell arguments**, **Script arguments** |
| **Command line** | `cmd.exe /c "<your command>"` | **Command line**, one line with its arguments |
| **Software (.exe/.msi)** | The EXE followed by your arguments, or `msiexec.exe /i "<installer>"` followed by them | **Script or installer path (relative to content)**, **Arguments or MSI properties**, **Generate installation log** (MSI only) |
| **Restart Windows** | A restart, then the next action | **Restart delay (seconds)**, 0 to 86400, default 0 |

Foundry adds nothing to your command: no silent switch, no `/norestart`, no PowerShell option. Enter them yourself, for example `/qn /norestart` for an MSI. A PowerShell script runs only if the execution policy of the deployed Windows allows it; enter `-NoProfile -ExecutionPolicy Bypass` in **PowerShell arguments** so the action does not depend on that policy.

| Execution setting | What it does | Default |
| --- | --- | --- |
| **Timeout (seconds)** | Longest time the action and every process it starts may run. 1 to 86400. | 1800 |
| **Success exit codes (comma-separated)** | Exit codes that mean the action succeeded. | 0 |
| **Restart-required exit codes (comma-separated)** | Exit codes that mean success, with a restart needed before the next action. | Empty; 3010 for a new **Software (.exe/.msi)** action |
| **Continue on error** | Runs the next actions when this one ends with an unexpected exit code. | Off |
| **Defer restart** | Postpones a requested restart until the next **Restart Windows** action or the end of the sequence. | Off |

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-post-installation-02-action-dialog.png`
- **Capture:** Show the **Add action** dialog for a **Software (.exe/.msi)** action with an imported MSI: name, installer type, package content, installer path, arguments, **Generate installation log**, the execution settings and the command preview.
{% endhint %}

## Prepare content

Import a folder when the script or installer needs other files, such as a transform or a CAB file. Relative paths are kept. **Import file** takes that one file only.

- Leave **Working directory (relative; empty uses content root)** empty to run from the root of the imported content, or enter a folder that exists inside it. Refer to imported files by a path relative to that folder. A **Command line** action without content runs from a temporary folder.
- `{ContentRoot}` and `{LogRoot}` in **Command preview** stand for folders chosen during deployment. You cannot type them in your arguments.
- Foundry copies the content to its library in `%LOCALAPPDATA%\Foundry\Packages\PreOobe` and never changes your source. To pick up a new version, select **Edit action** and import again. **Refresh** only checks that the library copy is still there.
- The content is deleted from the target device when the sequence ends. An application that needs its source for repair must copy it elsewhere itself.

<details>
<summary>Import limits</summary>

| Limit | Value |
| --- | --- |
| Path of a file inside the imported folder | 110 characters; 255 per name |
| Full path of a source file | 259 characters |
| Files and folders in one import | 10,000 files and 10,000 folders |
| Names | No `< > : " \| ? *`, no trailing dot or space, no reserved device name such as `CON` or `COM1` |
| Links | No symbolic link or junction, in the content or in the path that leads to it |
| Empty folder | A folder without any file is refused |
| Location | Not inside the Foundry library, and not a folder that contains it |
| Size | No fixed limit; the drive that holds the library needs enough free space |

</details>

## How actions run

Foundry waits for every process an action starts, not only the first one. An updater, a tray application or the installed application left running holds the action until its timeout.

| Result of an action | What Foundry does | With **Continue on error** |
| --- | --- | --- |
| Exit code in the success list | Runs the next action. | No effect |
| Exit code in the restart list | Restarts Windows, or defers the restart, then runs the next action. | No effect |
| Any other exit code | Marks the action failed, skips the remaining actions and stops. | Runs the remaining actions and ends with warnings |
| Timeout reached | Ends every process of the action and marks it failed, even when the first process returned 0. The result is uncertain, so the sequence stops. | Still stops |
| Exit code 1641: the installer started its own restart | Marks the action failed and stops. | Still stops |

Each list accepts up to 32 codes, zero or positive, and never 1641. A script that ends with a negative code always fails, and a PowerShell script must end with `exit <code>` to report a failure.

## Restarts

Never let a script or an installer restart Windows itself. An action that was running when Windows restarted is marked interrupted, is not run again, and stops the sequence. Instead:

- add a **Restart Windows** action where the restart must happen, or
- pass the installer's no-restart option and declare its code in **Restart-required exit codes**.

Foundry saves its progress before each planned restart and resumes at the next action.

{% hint style="warning" %}
Do not pass passwords, keys or tokens as arguments. The full command line of every action is saved in `C:\Windows\Temp\Foundry\State\PreOobe\plan.json` and its output in `C:\Windows\Temp\Foundry\Logs\PreOobe`. Both stay on the device after deployment.
{% endhint %}

## What the technician sees

After the restart, the **Foundry Post-installation** console lists Foundry's setup tasks, then your actions under the names you gave them, then `Cleanup`. Script output is not shown. [After the restart](../../foundry-deploy/after-the-restart.md) explains each line, the restarts and the hand-over.

## Check the result

- On the page, no warning bar is shown and every enabled action has **Available** or **Not required** under **Content status**.
- On a test device, every action ends with `[Succeeded]` and the console ends with `Post-installation completed.`

## Limits

- 1,000 actions. A name has 1 to 256 characters. Each command or argument field is one line of up to 8,191 characters.
- With the page turned off, your actions are kept but left out of new media. Foundry's own setup tasks still run.
- Imported files are not part of a [configuration](../deployment-profiles.md). On another workstation the action shows **Missing** until you import the same content there.
- With a [custom answer file](unattend.md), Foundry adds the command that starts this sequence to the deployment copy of the file.

## If something goes wrong

| What you see in Foundry OSD | What to do |
| --- | --- |
| "Content import failed. Check access, available space, file names and path lengths." | One message for every import limit. Shorten paths, copy the content to a plain local folder or free space, then import again. The Foundry OSD log names the limit, for example `PreOobe.InvalidPackagePath`. |
| "Add an enabled action or disable Post-installation." | Enable an action or turn the page off. |
| "Import the missing content for "\<name\>"." | Select **Edit action**, import the same file or folder, then **Save**. |
| "Edit "\<name\>" to correct its settings." | Open the action; the invalid field shows its own message. |
| "The configuration changed while you were editing. Open the action again." | The configuration was replaced while the dialog was open, for example by a sync. Reopen the action. |
| "The action was removed, but its cached content could not be deleted." | Nothing to do. The files stay in the library. |
| "Final media creation failed. Custom Windows image media preparation failed." with `PreOobe.InsufficientMediaSpace` | The content does not fit on the data partition of the USB drive, even when no custom image is configured. Free space on it or recreate the USB drive. |
| The same message with `PreOobe.PackageContentChanged` | A file in the library no longer matches what was imported. Import the content again. |

Failures on the target device are in [After the restart troubleshooting](../../troubleshooting/after-the-restart.md).

## Related

- [After the restart](../../foundry-deploy/after-the-restart.md)
- [After the restart troubleshooting](../../troubleshooting/after-the-restart.md)
- [Unattend (custom answer files)](unattend.md)
