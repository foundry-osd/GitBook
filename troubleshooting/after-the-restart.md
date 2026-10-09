# After the restart troubleshooting

Find what you see, then go to its section. Sections follow the order of events: the answer file in Foundry Deploy, the **Foundry Post-installation** console after the restart, then Windows Setup and the deployed device.

| What you see | Go to |
| --- | --- |
| "The selected answer file is unavailable, invalid, or incompatible..." | [Answer file refused](#answer-file-refused) |
| "This answer file conflicts with the configured Autopilot enrollment mode..." | [Autopilot conflict](#answer-file-autopilot-conflict) |
| "The configured default answer file is missing. Rebuild the boot media." | [Default missing](#default-answer-file-missing) |
| The deployment stops on **Validate answer file** | [Validate answer file](#the-deployment-stops-on-validate-answer-file) |
| "Post-installation staging failed..." | [Staging failed](#staging-failed) |
| No console after the restart, or "Post-installation could not initialize..." | [No console](#no-foundry-post-installation-console-appears) |
| "Post-installation stopped..." | [Sequence stopped](#post-installation-stopped) |
| `[Failed]` with `(exit <code>)` | [Exit code](#an-action-shows-failed-with-an-exit-code) |
| `[Running]` for a long time, then `[Failed]` | [Timeout](#an-action-stays-running-until-its-timeout) |
| `[Failed]` with `(exit 1641)` | [Exit 1641](#an-action-shows-failed-with-exit-1641) |
| `[Interrupted]` | [Interrupted](#an-action-shows-interrupted) |
| "Post-installation stopped..." and no `[Failed]` line | [No failed action](#the-sequence-stops-and-no-action-shows-failed) |
| `[Failed]` on `Install drivers`, `Remove AppX packages` or another Foundry task | [Foundry task](#a-foundry-task-shows-failed) |
| "Waiting for a Windows Setup restart." and the device stays on | [No restart](#waiting-for-restart) |
| The device restarts several times | [Several restarts](#the-device-restarts-several-times) |
| "Post-installation completed with warnings..." | [Warnings](#completed-with-warnings) |
| Windows Setup shows an error about the answer file, or ignores its settings | [Windows Setup](#windows-setup-fails-on-the-answer-file-or-ignores-its-settings) |
| Computer name, OOBE or Autopilot name from Foundry is missing | [Settings not applied](#foundry-settings-were-not-applied) |
| Windows is not activated | [Activation](#windows-is-not-activated) |
| Folders remain under `C:\Windows\Temp\Foundry` | [Leftover files](#leftover-files) |

Domain Join results are in [Domain Join troubleshooting](domain-join.md). After the restart, the evidence is on the deployed device, under `C:\Windows\Temp\Foundry`; see [Find the log of an action](#find-the-log-of-an-action). The Foundry Deploy log and the other logs are in [Log locations](logs-and-support.md#log-locations).

## "The selected answer file is unavailable, invalid, or incompatible with the selected Windows architecture. Choose another file or rebuild the media." <a href="#answer-file-refused" id="answer-file-refused"></a>

- **Where:** Foundry Deploy, on **Target device**, under **Answer file**. Nothing has been erased.
- **Cause:** one message for several cases.
  - No component of the file applies to the architecture of the selected Windows.
  - The copy of the file on the media is missing, damaged or cannot be decrypted.
  - On Domain Join media, the file does not set exactly one fixed computer name, or contains a `Microsoft-Windows-UnattendedJoin` component.
- **Fix:**
  1. Select a Windows image of the architecture the file was written for, another answer file, or **Use Foundry settings**.
  2. If no file of this media can be selected, have the administrator create the media again.
  3. For Domain Join, see [Domain Join troubleshooting](domain-join.md).
- **Collect:** `FoundryDeploy.log`.

## "This answer file conflicts with the configured Autopilot enrollment mode. Choose another file or change the media configuration." <a href="#answer-file-autopilot-conflict" id="answer-file-autopilot-conflict"></a>

- **Where:** Foundry Deploy, on **Target device**. Nothing has been erased.
- **Cause:** the media uses Windows Autopilot with a JSON profile or the interactive upload, and the file contains a setting that prevents enrollment, such as a local account, automatic logon or a skipped OOBE.
- **Fix:** select another file or **Use Foundry settings**. The administrator removes the [blocking settings](../foundry-osd/customization/unattend.md#settings-that-block-windows-autopilot), selects **Refresh source** and creates the media again.

## "The configured default answer file is missing. Rebuild the boot media." <a href="#default-answer-file-missing" id="default-answer-file-missing"></a>

- **Where:** Foundry Deploy, on **Target device**.
- **Cause:** the media configuration names a default answer file that is not on the media.
- **Fix:** in Foundry OSD, choose a file or **Use Foundry settings** under **Deployment default**, then create the media again.

## The deployment stops on Validate answer file

- **Where:** Foundry Deploy, step **Validate answer file**, before the disk is erased. The step shows one of these messages, always in English.
- **Cause:** the deployment has tasks to run after the restart, so Foundry must add its command to the file, and the file does not follow the [rules for that](../foundry-osd/customization/unattend.md#when-foundry-adds-its-command). These rules are not checked at import.

| Message | What is wrong in the file |
| --- | --- |
| "The answer file contains duplicate specialize passes." | More than one `<settings pass="specialize">` block. |
| "The specialize pass is already marked as processed." | The block carries `wasPassProcessed`. |
| "The Windows Deployment component conflicts with the selected image architecture." | `Microsoft-Windows-Deployment` appears more than once in the `specialize` pass, or its `processorArchitecture` is not that of the selected Windows. |
| "Duplicate RunSynchronous lists are not supported." | More than one `RunSynchronous` list in that component. |
| "RunSynchronous orders must be unique integers from 1 through 500." | An `Order` is missing, repeated or out of range. |
| "No RunSynchronous order remains after the existing commands." | A command already uses `Order` 500. |
| "The answer file contains duplicate Foundry post-installation commands.", "The existing Foundry command conflicts with automatic post-installation integration.", or another message that names the Foundry command | The file already holds a command described as `Foundry PostInstall` or calling `Launch.cmd`. Remove it; Foundry adds its own. |
| "The answer file conflicts with the selected Autopilot enrollment mode." | See [Autopilot conflict](#answer-file-autopilot-conflict). |
| "The selected answer file is unavailable, invalid, or incompatible with the selected Windows architecture." | See [Answer file refused](#answer-file-refused). |
| "The custom answer-file computer name differs from the computer name confirmed for the domain join." | See [Domain Join troubleshooting](domain-join.md). |

- **Fix:** select another file or **Use Foundry settings** to deploy now. The administrator corrects the source file, selects **Refresh source** in Foundry OSD and creates the media again.
- **Collect:** `FoundryDeploy.log`.

## "Post-installation staging failed. Verify the runtime, payloads and Windows answer file before retrying." <a href="#staging-failed" id="staging-failed"></a>

- **Where:** Foundry Deploy, step **Apply Windows image** or **Prepare setup tasks**. The disk is already erased: the device has no usable Windows.
- **Cause:**
  - In **Apply Windows image**: the image contains its own answer file. Foundry refuses `Windows\Panther\Unattend\unattend.xml`, `Windows\Panther\Unattend\autounattend.xml` and an `UnattendFile` value under `HKLM\SYSTEM\Setup`, which Windows would use instead of Foundry's file, and a `Windows\Panther\unattend.xml` that cannot take Foundry's command. This happens with custom Windows images.
  - In **Prepare setup tasks**: the deployment media was removed or changed during the deployment, a file could not be copied to the disk, or the answer file on the disk cannot take Foundry's command.
- **Fix:**
  1. Keep the deployment media connected until the deployment ends.
  2. For a custom image, remove the answer file from the reference installation and capture the image again. See [Custom Windows images](../foundry-osd/customization/custom-windows-images.md).
  3. Deploy again from the start. A deployment cannot be resumed.
- **Collect:** `FoundryDeploy.log`.

## No Foundry Post-installation console appears

- **Where:** after the restart. Windows goes straight to its first setup screens or to the sign-in screen.
- **Cause:**
  - Nothing had to run. Foundry Deploy did not list **Prepare setup tasks**, or showed it as skipped with "No post-installation tasks are required." This is normal, for example with a Volume license, a custom image or a custom answer file and no Post-installation action.
  - The console opened and closed at once with "Post-installation could not initialize. Check the staged plan, journal, and runtime files.": `plan.json` is missing, damaged or was changed after the deployment. No log is written in this case.
  - Windows Setup did not reach Foundry's command. With a custom answer file, the file's own `specialize` commands run first.
- **Fix:**
  1. Look for `C:\Windows\Temp\Foundry\State\PreOobe`. If the folder does not exist, nothing was planned.
  2. If `execution-result.json` there still reads `"status": "Pending"` and `Logs\PreOobe\Foundry.PostInstall.log` does not exist, the sequence never started. Open `C:\Windows\Panther\unattend.xml` and check that it contains a command described as `Foundry PostInstall`, and which commands precede it.
  3. Correct the cause and deploy again. The sequence cannot be started by hand: outside Windows Setup the program answers "This application must be started by Windows Setup as Local System."
- **Collect:** `State\PreOobe\execution-result.json`, and the Windows Setup logs `C:\Windows\Panther\setupact.log`, `C:\Windows\Panther\setuperr.log` and `C:\Windows\Panther\UnattendGC\setupact.log`. These three are standard Windows files, not written by Foundry.

## "Post-installation stopped. Review the execution result and logs before continuing Windows Setup." <a href="#post-installation-stopped" id="post-installation-stopped"></a>

- **Where:** last line of the console, in red. The console closes at once, without the 10-second countdown, and the remaining lines show `[Skipped]`.
- **Cause:** a task failed and the sequence was not allowed to continue, or Foundry cannot tell whether a task finished.
- **Fix:**
  1. Do not hand over the device.
  2. Open `C:\Windows\Temp\Foundry\State\PreOobe\execution-result.json`. Under `actions`, find the entry whose `status` is `Failed` and read its `exitCode` and `failureCode`.
  3. Find the name behind that identifier: see [Find the log of an action](#find-the-log-of-an-action).
  4. Go to the section for its `failureCode`.
  5. Have the administrator correct the action in Foundry OSD and create the media again, then redeploy. Nothing runs the sequence a second time on this device.

| `failureCode` | Section |
| --- | --- |
| `process_exit_code` | [Exit code](#an-action-shows-failed-with-an-exit-code) |
| `execution_uncertain` | [Timeout](#an-action-stays-running-until-its-timeout), [Exit 1641](#an-action-shows-failed-with-exit-1641) or [Interrupted](#an-action-shows-interrupted) |
| `entry_point_missing`, `working_directory_missing` | The script, the installer or the working directory is no longer in the content, for example because an earlier action that shares it moved or deleted it. |
| `process_start_failed` | Windows could not start the program, for example an installer built for another architecture. |
| `process_failed`, `appx_inventory_truncated`, `activation_failed`, `cleanup_uncertain` | [Foundry task](#a-foundry-task-shows-failed) |
| No failed entry | [No failed action](#the-sequence-stops-and-no-action-shows-failed) |

- **Collect:** the whole `C:\Windows\Temp\Foundry\State\PreOobe` and `C:\Windows\Temp\Foundry\Logs\PreOobe` folders. Read the warning in [Find the log of an action](#find-the-log-of-an-action) before you share them.

## An action shows Failed with an exit code

- **Where:** `- [Failed] <name> 00:12 (exit 1603)`.
- **Cause:** the script or installer returned a code that is in neither **Success exit codes** nor **Restart-required exit codes**. With **Continue on error** off, the sequence stops; with it on, the next actions run and the console ends with [warnings](#completed-with-warnings).
- **Fix:**
  1. Read `output.log` of the action, and the MSI log when **Generate installation log** is on.
  2. If the code is a normal result of this installer, add it to the right list in Foundry OSD.
  3. For a PowerShell script, look in `output.log` for an execution policy error. `-ExecutionPolicy Bypass` in **PowerShell arguments** removes the dependency on the policy of the image.
  4. Correct the action, create the media again and redeploy.
- **Collect:** `Logs\PreOobe\<action id>\Process\output.log`.

## An action stays Running until its timeout

- **Where:** the line stays `[Running]` for the whole **Timeout (seconds)** of the action, 30 minutes by default, then shows `[Failed]`, sometimes with `(exit 0)`. The sequence stops.
- **Cause:**
  - The program waits for an answer. Actions have no window and no keyboard input.
  - The installer finished but left a process running: the application itself, an updater or a tray program. Foundry waits for all of them.
  - The work really takes longer than the timeout.
- **Fix:** make the package fully silent and stop it from launching anything at the end. Raise **Timeout (seconds)** only for an installation that is long by nature. **Continue on error** does not help: after a timeout the result is uncertain and the sequence always stops.
- **Collect:** `execution-result.json`, where the action has `failureCode` `execution_uncertain`, and its `output.log`. The content of this action is left on the disk: see [Leftover files](#leftover-files).

## An action shows Failed with exit 1641

- **Where:** `- [Failed] <name> 01:40 (exit 1641)`, then the sequence stops.
- **Cause:** the installer started a restart by itself. Foundry does not accept it, with or without **Continue on error**.
- **Fix:** pass the installer's no-restart option, `/norestart` for an MSI, and declare 3010 in **Restart-required exit codes** so that Foundry performs the restart.

## An action shows Interrupted

- **Where:** the device restarted or lost power in the middle of an action. When the console comes back, that action shows `[Interrupted]`, the following ones `[Skipped]`, and the last line is "Post-installation stopped...".
- **Cause:** a script ran `shutdown /r` or `Restart-Computer`, an installer restarted Windows without returning 1641, or the power was cut. Foundry cannot know whether the action finished and does not run it again.
- **Fix:**
  1. Check on the device whether the work of the action was done, then redeploy.
  2. Have the administrator replace the restart by a **Restart Windows** action or a restart exit code. See [Post-installation](../foundry-osd/customization/post-installation.md#restarts).
- **Collect:** `execution-result.json`, where `status` is `Interrupted` and `unsafeActionId` names the action.

## The sequence stops and no action shows Failed

- **Where:** "Post-installation stopped..." with every line still `[Waiting]`, or between two actions.
- **Cause:** Foundry found its own files in an unexpected state and refused to run. Before the first action it compares the imported content on the disk with what was deployed: a missing, changed or added file stops the sequence. `plan.json` or `execution-result.json` that was edited, deleted or does not match the deployment has the same effect.
- **Fix:** redeploy. Do not edit the files under `C:\Windows\Temp\Foundry` between the deployment and the first sign-in.
- **Collect:** `Logs\PreOobe\Foundry.PostInstall.log`, line `Post-installation stopped; failure type <type>`.

## A Foundry task shows Failed

- **Where:** `[Failed]` on a line that is not one of the administrator's actions.

| Task | Effect on the sequence | Where to look |
| --- | --- | --- |
| `Install drivers` | Stops. None of the administrator's actions run. | `Logs\PreOobe\surface-driverpack.log` for a Surface pack. Select another driver source in Foundry Deploy and redeploy. |
| `Remove AppX packages`, `Remove AI components` | A package that cannot be removed is only a warning. The task fails and stops the sequence when Windows cannot list the installed packages. | `Logs\PreOobe\appx-servicing.log` |
| `Configure network` | A profile or certificate that cannot be imported is only a warning. | Wi-Fi and Ethernet settings in Windows |
| `Activate Windows` | Continues, with warnings. It stops only when the task does not end within 60 seconds. | [Activation](#windows-is-not-activated) |
| `Join domain and place computer`, `Verify domain membership` | Continues, with warnings. | [Domain Join troubleshooting](domain-join.md) |
| `Cleanup` | Ends with warnings. | [Leftover files](#leftover-files) |

## "Waiting for a Windows Setup restart." <a href="#waiting-for-restart" id="waiting-for-restart"></a>

- **Where:** last line of the console before every planned restart. It is a problem only when the device does not restart.
- **Cause:** Foundry saved its progress and asked Windows Setup for a restart that did not happen, or the console was started again before it happened.
- **Fix:** restart the device once. No task is running at this point, so nothing is lost. If the console does not come back with `Resuming after restart`, collect the files and redeploy.
- **Collect:** `execution-result.json`, where `status` is `AwaitingRestart`, with its `restartCount`.

## The device restarts several times

- **Where:** the console reopens with `Resuming after restart` more than once.
- **Cause:** not a loop. Each **Restart Windows** action, each action that returns a restart-required exit code and Domain Join restart Windows once, and a driver or AppX task can ask for restarts too. Every action runs a single time, and Foundry stops the sequence after 1,024 restarts.
- **Fix:** to group restarts, the administrator turns on **Defer restart** for the actions that return a restart code. The restart then happens at the next **Restart Windows** action or at the end of the sequence.

## "Post-installation completed with warnings. Review the execution result and logs." <a href="#completed-with-warnings" id="completed-with-warnings"></a>

- **Where:** last line of the console, in yellow, with `Warnings:` above 0. Windows Setup continues after the 10-second countdown.
- **Cause:** one or more of these.
  - An action with **Continue on error** failed.
  - A network profile or a certificate was not imported, or an AppX package was not removed.
  - Windows activation failed.
  - A Domain Join part is not `Succeeded`.
  - Temporary files could not be deleted.
- **Fix:** open `execution-result.json`, where `status` is `CompletedWithErrors`, and read the `status` of each entry under `actions`. Decide for each failure whether the device can be handed over. For Domain Join, see [Domain Join troubleshooting](domain-join.md).
- **Collect:** `State\PreOobe\execution-result.json` and `Logs\PreOobe`.

## Windows Setup fails on the answer file or ignores its settings

- **Where:** Windows Setup, after the restart, with a custom answer file: an error screen about the answer file, or a deployed Windows without the expected settings.
- **Cause:** a setting that is not valid for this Windows image, a component with the wrong `processorArchitecture`, or a command of the file that failed. Foundry checks the structure of the file, not what Windows makes of it.
- **Fix:**
  1. Validate the file with Windows System Image Manager against the same image.
  2. Compare it with the file Windows used, `C:\Windows\Panther\unattend.xml`.
  3. Test the corrected file on a virtual machine before you create the media again.
- **Collect:** `C:\Windows\Panther\setupact.log`, `C:\Windows\Panther\setuperr.log` and `C:\Windows\Panther\UnattendGC\setupact.log`. These are standard Windows Setup logs, not written by Foundry. Do not share `unattend.xml` itself without removing its secrets.

## Foundry settings were not applied

- **Where:** the deployed Windows does not have the computer name, the OOBE choices or the activation you expected, or the device has no name in Windows Autopilot.
- **Cause:** a custom answer file was selected. Foundry then leaves the computer name, the OOBE page, the Autopilot name upload and Windows activation to the file. This is by design: see [What a custom answer file overrides](../foundry-osd/customization/unattend.md#what-a-custom-answer-file-overrides).
- **Fix:** put the setting in the answer file, or select **Use Foundry settings** on **Target device**.
- **Collect:** the deployment summary, which shows the selected **Answer file**. For the Autopilot name, `FoundryDeploy.log` contains "Autopilot computer name assignment skipped because no final Foundry Deploy computer name is available."

## Windows is not activated

- **Where:** **Settings > System > Activation** in the deployed Windows.
- **Cause:** it depends on the `Activate Windows` line of the console.

| Console | Cause |
| --- | --- |
| No `Activate Windows` line | The deployment used a custom Windows image, a Volume license or a custom answer file. Foundry does not activate these. |
| `[Succeeded] Activate Windows` | The task ran and found nothing it may change: the edition is not Home or Pro, Windows uses volume licensing, the firmware has no OEM key, or its key is for another edition, for example a Home key on a device deployed with Pro. |
| `[Failed] Activate Windows` | Foundry tried to install the firmware key or to activate Windows and it failed, for example without Internet access. |

- **Fix:** connect the device to the Internet and activate from **Settings**, or apply your organization's licensing.
- **Collect:** `execution-result.json`, entry `windows-oem-activation` under `actions`.

## Files remain under C:\Windows\Temp\Foundry <a href="#leftover-files" id="leftover-files"></a>

- **Where:** the deployed device, after the first sign-in.

| Folder | Content | After the sequence |
| --- | --- | --- |
| `Runtime\PreOobe` | The Post-installation program | Kept |
| `State\PreOobe` | `plan.json`, `execution-result.json`, Domain Join results | Kept |
| `Logs\PreOobe` | Logs | Kept |
| `Payloads` | Imported content, driver installer, network profiles, join credentials | Deleted when the sequence ends |
| `Work\PreOobe` | Temporary working folders of the actions | Deleted when the sequence ends |

- **Cause:** `Payloads` or `Work` still holds files when an action timed out or was interrupted, because one of its processes may still use them, or when the files stayed locked after three attempts. Nothing deletes them later.
- **Fix:** once you have what you need, delete `Payloads` and `Work` by hand. `State\PreOobe\plan.json` holds the command line of every action, and `C:\Windows\Panther\unattend.xml` also stays on the device: delete them if they contain anything sensitive.
- **Collect:** `execution-result.json`, where `payloadDispositions` shows `CleanupPending` for what was left.

## Find the log of an action

1. Open `C:\Windows\Temp\Foundry\State\PreOobe\plan.json`. Under `actions`, each entry has an `id` and a `name`. The administrator's actions have a 32-character identifier; Foundry's tasks have a short one such as `driver-pack` or `cleanup`.
2. Open `C:\Windows\Temp\Foundry\Logs\PreOobe\<id>\Process\output.log`. It holds the standard output of the action, then its error output, each limited to 2,097,152 characters. Foundry's own tasks have no such file.
3. For an MSI with **Generate installation log**, the installer log is `<installer name>.log` in `Logs\PreOobe\<id>`.
4. In `State\PreOobe\execution-result.json`, the entry with the same identifier under `actions` gives `status`, `exitCode`, `failureCode` and the start and end times.

`output.log` is written when the action ends. An action cut short by a power loss leaves none.

{% hint style="warning" %}
`plan.json` contains the full command line and arguments of every action, and `output.log` everything the script printed. Read them before you send them to anyone.
{% endhint %}

<details>
<summary>Values of status in execution-result.json</summary>

| `status` | Meaning |
| --- | --- |
| `Pending` | Deployed, never started. |
| `Running` | An action is in progress, or the device stopped during one. |
| `AwaitingRestart` | Progress saved, waiting for a planned restart. |
| `Completing` | Running `Cleanup`. |
| `Succeeded` | Everything succeeded. |
| `CompletedWithErrors` | Finished with warnings. |
| `Failed` | Stopped on a failure. |
| `Interrupted` | Stopped because an action was cut short by a restart. |

</details>
