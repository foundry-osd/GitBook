# After the restart troubleshooting

Find what you see, then go to its section. In the **Foundry Post-installation** console, the status line is the coloured line under the list of actions, above `Log:`.

## Symptom index

### Before the restart, in Foundry Deploy

Answer-file and staging failures in Foundry Deploy are in [Windows deployment troubleshooting](deployment.md).

| What you see | Go to |
| --- | --- |
| A message under **Answer file** on **Target device** | [Windows deployment troubleshooting](deployment.md) |
| The deployment stops on **Validate answer file** | [Windows deployment troubleshooting](deployment.md) |
| "Post-installation staging failed..." | [Windows deployment troubleshooting](deployment.md) |

### After the restart

| What you see | Go to |
| --- | --- |
| No console, or "Post-installation could not initialize..." | [No console](#no-foundry-post-installation-console-appears) |
| The console closed before you could read it | [Console closed](#console-closed) |
| "Post-installation stopped..." | [Sequence stopped](#post-installation-stopped) |
| `[Failed]` with `(exit <code>)` | [Exit code](#an-action-shows-failed-with-an-exit-code) |
| `[Running]` for a long time then `[Failed]`, or `(exit 1641)` | [Timeout or 1641](#action-uncertain) |
| `[Interrupted]` | [Interrupted](#an-action-shows-interrupted) |
| "Post-installation stopped..." and no `[Failed]` line | [No failed action](#the-sequence-stops-and-no-action-shows-failed) |
| `[Failed]` on `Install drivers`, `Remove AppX packages` or another Foundry task | [Foundry task](#a-foundry-task-shows-failed) |
| "Waiting for a Windows Setup restart." and the device stays on | [No restart](#waiting-for-restart) |
| "Post-installation completed with warnings..." | [Warnings](#completed-with-warnings) |
| Windows Setup shows an error about the answer file, or ignores its settings | [Windows Setup](#windows-setup-fails-on-the-answer-file-or-ignores-its-settings) |
| Computer name, OOBE choices or Autopilot name from Foundry are missing | [What a custom answer file overrides](../foundry-osd/customization/unattend.md#what-a-custom-answer-file-overrides) |
| Windows is not activated | [Activation](#windows-is-not-activated) |
| Folders remain under `C:\Windows\Temp\Foundry` | [Leftover files](#leftover-files) |

Domain Join results are in [Domain Join troubleshooting](domain-join.md). Other logs are in [Log locations](logs-and-support.md#log-locations).

## Reach the files

Every file named below is on the Windows volume of the deployed device, under `C:\Windows\Temp\Foundry`. Read them on that device once Windows is reachable, as an administrator: the folders are restricted to administrators. Foundry does not control what Windows Setup shows after a stop, so this page cannot promise that Windows gets that far.

## No Foundry Post-installation console appears

- **Where:** after the restart. Windows goes straight to its first setup screens or to the sign-in screen.
- **Cause:**
  - Nothing had to run. Foundry Deploy did not list **Prepare setup tasks**, or showed it as skipped with "No post-installation tasks are required." This is normal, for example with a Volume license, a custom image or a custom answer file and no Post-installation action.
  - The console opened and closed at once with "Post-installation could not initialize. Check the staged plan, journal, and runtime files.": `plan.json` is missing or damaged. No log is written in this case.
  - Windows Setup did not reach Foundry's command. With a custom answer file, the file's own `specialize` commands run first.
- **Fix:** on the deployed device, once Windows is reachable:
  1. Look for `C:\Windows\Temp\Foundry\State\PreOobe`. If the folder does not exist, nothing was planned.
  2. If `execution-result.json` there still reads `"status": "Pending"` and `Logs\PreOobe\Foundry.PostInstall.log` does not exist, the sequence never started. Open `C:\Windows\Panther\unattend.xml` and check that it contains a command described as `Foundry PostInstall`, and which commands precede it.
  3. Correct the cause and deploy again. The sequence cannot be started by hand: outside Windows Setup the program answers "This application must be started by Windows Setup as Local System."
- **Collect:** `State\PreOobe\execution-result.json`, and the Windows Setup logs `C:\Windows\Panther\setupact.log`, `C:\Windows\Panther\setuperr.log` and `C:\Windows\Panther\UnattendGC\setupact.log`. These three are standard Windows files, not written by Foundry.

## The console closed before I could read it <a href="#console-closed" id="console-closed"></a>

- **Where:** the console window. After a stop it closes at once; after a completion it closes when the 10-second countdown ends.
- **Cause:** by design. The result of every line is saved in `execution-result.json`.
- **Fix:** on the deployed device, once Windows is reachable:
  1. Open `C:\Windows\Temp\Foundry\State\PreOobe\execution-result.json` and read the top-level `status`: `Succeeded`, `CompletedWithErrors` (warnings), `Failed` or `Interrupted` (stopped).
  2. Under `actions`, find each entry whose `status` is `Failed` and read its `exitCode` and `failureCode`.
  3. Find the name behind its identifier: see [Find the log of an action](#find-the-log-of-an-action).
  4. Go to the section for its `failureCode`.

| `failureCode` | Section |
| --- | --- |
| `process_exit_code` | [Exit code](#an-action-shows-failed-with-an-exit-code) |
| `execution_uncertain` | [Timeout or 1641](#action-uncertain), or [Interrupted](#an-action-shows-interrupted) |
| `entry_point_missing`, `working_directory_missing` | The script, the installer or the working directory is no longer in the content, for example because an earlier action that shares it moved or deleted it. |
| `process_start_failed` | Windows could not start the program, for example an installer built for another architecture. |
| `process_failed`, `appx_inventory_truncated`, `activation_failed`, `cleanup_uncertain` | [Foundry task](#a-foundry-task-shows-failed) |
| No failed entry | [No failed action](#the-sequence-stops-and-no-action-shows-failed) |

- **Collect:** the `State\PreOobe` and `Logs\PreOobe` folders. Read the warning in [Find the log of an action](#find-the-log-of-an-action) before you share them.

## "Post-installation stopped. Review the execution result and logs before continuing Windows Setup." <a href="#post-installation-stopped" id="post-installation-stopped"></a>

- **Where:** the status line, in red. The remaining lines show `[Skipped]` and the window closes at once, without the 10-second countdown.
- **Cause:** a Foundry task or an action failed and the sequence was not allowed to continue, or Foundry cannot tell whether it finished.
- **Fix:**
  1. Do not hand over the device.
  2. Read the result as described in [Console closed](#console-closed).
  3. Have the administrator correct the cause in Foundry OSD and create the media again, then redeploy. Nothing runs the sequence a second time on this device.
- **Collect:** the `State\PreOobe` and `Logs\PreOobe` folders.

## An action shows Failed with an exit code

- **Where:** `- [Failed] <name> 00:12 (exit 1603)`.
- **Cause:** the script or installer returned a code that is in neither **Success exit codes** nor **Restart-required exit codes**. With **Continue on error** off, the sequence stops; with it on, the next actions run and the console ends with [warnings](#completed-with-warnings).
- **Fix:**
  1. Read `output.log` of the action, and the MSI log when **Generate installation log** is on.
  2. If the code is a normal result of this installer, add it to the right list in Foundry OSD.
  3. If `output.log` of a PowerShell script shows that the execution policy refused the script, enter `-NoProfile -ExecutionPolicy Bypass` in **PowerShell arguments**.
  4. Correct the action, create the media again and redeploy.
- **Collect:** `Logs\PreOobe\<action id>\Process\output.log`.

## An action stays Running until its timeout, or fails with exit 1641 <a href="#action-uncertain" id="action-uncertain"></a>

- **Where:** the line stays `[Running]` for the whole **Timeout (seconds)** of the action, 30 minutes by default, then shows `[Failed]`, sometimes with `(exit 0)`. Or it shows `[Failed]` with `(exit 1641)`. In both cases the sequence stops, with or without **Continue on error**.
- **Cause:** Foundry cannot tell whether the action finished.
  - The program waits for an answer. Actions have no window and no keyboard input.
  - The installer finished but left a process running: the application itself, an updater or a tray program. Foundry waits for all of them.
  - The work really takes longer than the timeout.
  - Exit 1641: the installer started a restart by itself.
- **Fix:** make the package fully silent and stop it from launching anything at the end. Raise **Timeout (seconds)** only for an installation that is long by nature. For 1641, pass the installer's no-restart option, `/norestart` for an MSI, and declare 3010 in **Restart-required exit codes** so that Foundry performs the restart.
- **Collect:** `execution-result.json`, where the action has `failureCode` `execution_uncertain`, and its `output.log`. After a timeout the content of the action is left on the disk: see [Leftover files](#leftover-files).

## An action shows Interrupted

- **Where:** the device restarted or lost power in the middle of an action. When the console comes back, that action shows `[Interrupted]`, the following ones `[Skipped]`, and the status line is "Post-installation stopped...".
- **Cause:** a script ran `shutdown /r` or `Restart-Computer`, an installer restarted Windows without returning 1641, or the power was cut. Foundry cannot know whether the action finished and does not run it again.
- **Fix:**
  1. Check on the device whether the work of the action was done, then redeploy.
  2. Have the administrator replace the restart by a **Restart Windows** action or a restart exit code. See [Post-installation](../foundry-osd/customization/post-installation.md#restarts).
- **Collect:** `execution-result.json`, where `status` is `Interrupted` and `unsafeActionId` names the action.

## The sequence stops and no action shows Failed

- **Where:** "Post-installation stopped..." with every line still `[Waiting]`, or between two actions.
- **Cause:** Foundry found its own files in an unexpected state and refused to run. Before the first action it compares the imported content on the disk with what was deployed: a missing, changed or added file stops the sequence. An edited `plan.json`, or an `execution-result.json` that was edited, deleted or does not match the deployment, has the same effect.
- **Fix:** redeploy. Do not edit the files under `C:\Windows\Temp\Foundry` between the deployment and the first sign-in.
- **Collect:** `Logs\PreOobe\Foundry.PostInstall.log`, line `Post-installation stopped; failure type <type>`.

## A Foundry task shows Failed

- **Where:** `[Failed]` on a line that is not one of the administrator's actions.
- **Cause:** it depends on the task.

| Foundry task | Why it fails, and the effect | Where to look |
| --- | --- | --- |
| `Install drivers` | The driver installer returned an error or ran out of time. The sequence stops and no administrator's action runs. | `Logs\PreOobe\surface-driverpack.log` for a Surface pack |
| `Remove AppX packages`, `Remove AI components` | Windows could not list the installed packages. The sequence stops. A package that cannot be removed is only a warning. | `Logs\PreOobe\appx-servicing.log` |
| `Activate Windows` | See [Activation](#windows-is-not-activated). The sequence continues, with warnings, unless the task takes more than 60 seconds. | `execution-result.json` |
| `Join domain and place computer`, `Verify domain membership` | See [Domain Join troubleshooting](domain-join.md). The sequence continues, with warnings. | The Domain Join lines |
| `Cleanup` | Temporary files or settings could not be removed. The sequence ends with warnings. | [Leftover files](#leftover-files) |

- **Fix:** for `Install drivers` and the removals, deploy again. If `Install drivers` fails again, select another driver source in Foundry Deploy. For the other tasks, follow the linked section.
- **Collect:** the `State\PreOobe` and `Logs\PreOobe` folders.

## "Waiting for a Windows Setup restart." <a href="#waiting-for-restart" id="waiting-for-restart"></a>

- **Where:** the status line before every planned restart. It is a problem only when the device does not restart.
- **Cause:** Foundry saved its progress and asked Windows Setup for a restart that did not happen, or the console was started again before it happened.
- **Fix:** restart the device once. Nothing is running at this point, so nothing is lost. If the console does not come back with `Resuming after restart`, collect the files and redeploy.
- **Collect:** `execution-result.json`, where `status` is `AwaitingRestart`, with its `restartCount`.

## "Post-installation completed with warnings. Review the execution result and logs." <a href="#completed-with-warnings" id="completed-with-warnings"></a>

- **Where:** the status line, in yellow, with `Warnings:` above 0 on the line below. Windows Setup continues after the 10-second countdown.
- **Cause:** one or more of these.
  - An action with **Continue on error** failed.
  - A network profile or a certificate was not imported, or an AppX package was not removed.
  - Windows activation failed.
  - A Domain Join part is not `Succeeded`.
  - Temporary files could not be deleted.
- **Fix:** on the deployed device, open `execution-result.json`, where `status` is `CompletedWithErrors`, and read the `status` of each entry under `actions`. Decide for each failure whether the device can be handed over. For Domain Join, see [Domain Join troubleshooting](domain-join.md).
- **Collect:** `State\PreOobe\execution-result.json` and `Logs\PreOobe`.

## Windows Setup fails on the answer file or ignores its settings

- **Where:** Windows Setup, after the restart, with a custom answer file: an error screen about the answer file, or a deployed Windows without the expected settings.
- **Cause:** a setting that is not valid for this Windows image, a component with the wrong `processorArchitecture`, or a command of the file that failed. Foundry checks the structure of the file, not what Windows makes of it. A computer name, OOBE choices or an Autopilot name set in Foundry are not applied at all with a custom answer file: see [What a custom answer file overrides](../foundry-osd/customization/unattend.md#what-a-custom-answer-file-overrides).
- **Fix:**
  1. Validate the file with Windows System Image Manager against the same image.
  2. Compare it with the file Windows used, `C:\Windows\Panther\unattend.xml`.
  3. Test the corrected file on a virtual machine before you create the media again.
- **Collect:** `C:\Windows\Panther\setupact.log`, `C:\Windows\Panther\setuperr.log` and `C:\Windows\Panther\UnattendGC\setupact.log`. These are standard Windows Setup logs, not written by Foundry. Do not share `unattend.xml` itself without removing its secrets.

## Windows is not activated

- **Where:** **Settings > System > Activation** in the deployed Windows.
- **Cause:** it depends on the `Activate Windows` line of the console.

| Console | Cause |
| --- | --- |
| No `Activate Windows` line | The deployment used a custom Windows image, a Volume license or a custom answer file. Foundry does not activate these. |
| `[Succeeded] Activate Windows` | The task ran and found nothing it may change: Windows is already activated, the edition is not Home or Pro, Windows uses volume licensing, the firmware has no OEM key, its key is for another edition (for example a Home key on a device deployed with Pro), or the installed product key is neither the default setup key nor the firmware's OEM key. |
| `[Failed] Activate Windows` | Foundry tried to install the firmware key or to activate Windows and it failed, for example without Internet access. |

- **Fix:** connect the device to the Internet and activate from **Settings**, or apply your organization's licensing.
- **Collect:** `execution-result.json`, entry `windows-oem-activation` under `actions`.

## Files remain under C:\Windows\Temp\Foundry <a href="#leftover-files" id="leftover-files"></a>

- **Where:** the deployed device, after the first sign-in. Foundry Post-installation uses these folders:

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

On the deployed device, once Windows is reachable:

1. Open `C:\Windows\Temp\Foundry\State\PreOobe\plan.json`. Under `actions`, each entry has an `id` and a `name`. The administrator's actions have a 32-character identifier; Foundry's tasks have a short one such as `driver-pack` or `cleanup`.
2. Open `C:\Windows\Temp\Foundry\Logs\PreOobe\<id>\Process\output.log`. It holds the standard output of the action, then its error output, each limited to 2,097,152 characters. Foundry's tasks have no such file.
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
