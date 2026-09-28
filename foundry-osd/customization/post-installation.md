# Post-installation

{% hint style="info" %}
Use matching Foundry OSD and runtime assets for Post-installation. Validate the complete deployment on representative Windows images and hardware before production use.
{% endhint %}

Use **Customization > Post-installation** to run your own scripts, commands and application installers after Windows installation and before OOBE. The separate [OOBE page](oobe.md) configures the Windows first-run experience.

Foundry runs selected built-in tasks first, then your enabled actions in the order shown, followed by cleanup. Built-in tasks cannot be moved or deleted from the custom action list. The interactive Autopilot registration assistant runs separately during OOBE.

Additional update workflows added on this page require your own scripts or packages; there is no automatic “install all updates” action. Existing Foundry driver and firmware provisioning remains available.

## Add and order actions

Use **Add action** to create an action, then select rows to edit, enable, disable, remove or reorder them. **Command preview** shows the command that will run. Select **Save** and correct any highlighted fields.

1. Enable the page using its header switch.
2. Select **Add action** in the CommandBar and choose **PowerShell script (.ps1)**, **Command line**, **Software (.exe/.msi)** or **Restart Windows**.
3. Give the action a recognizable name. For scripts and applications, import a file or a folder containing all required files.
4. Select the script or installer relative to that content, review the automatically detected installer type and working directory, then enter its arguments. PowerShell has separate **PowerShell arguments** (before the script path) and **Script arguments** fields. For **Command line**, enter the complete command and its arguments; content is optional. For MSI installers, optionally enable **Generate installation log**.
5. For scripts, commands and software, review the timeout, success/restart return codes, **Continue on error** and **Defer restart**. For a Restart action, set **Restart delay (seconds)**; use `0` to restart immediately. Then save.
6. Select a row and use **Move up**, **Move down**, **Edit action**, **Enable/Disable** or **Remove**. Removing an action deletes its cached content only when no remaining action or saved local profile references it. Disabled actions still retain their content. Original source files are never deleted.
7. Resolve missing-content or validation messages before creating media.

An enabled page requires at least one enabled, valid action with available content before ISO or USB media can be created. An empty list or a list containing only disabled actions needs attention. Add or enable an action, or disable the page. Missing-content and invalid-settings warnings name the action to fix.

Disabling this page disables its configuration controls and custom actions; the page switch and documentation remain available. Selected built-in tasks still run when custom actions are disabled.

If cached content cannot be safely removed, Foundry reports the cleanup problem. The action remains removed, but cached files may remain on disk.

To update a package, edit its action and import the changed source again. **Refresh** checks cached content availability; it does not import changes from the original source folder.

| Action | Configuration |
| --- | --- |
| PowerShell script (.ps1) | A `.ps1` file and optional sibling content, executed by Windows PowerShell 5.1. You supply host arguments and script arguments separately. |
| Command line | A command interpreted by `cmd.exe`; optionally import files it needs. |
| Software (.exe/.msi) | An EXE or MSI with your arguments, properties or transforms. Foundry supplies only the executable path or `msiexec.exe /i` launch command, plus optional MSI logging. Installer type is detected from the selected file and is read-only. |
| Restart Windows | An explicit restart at this position, optionally preceded by a countdown, followed by resumption at the next action. No process arguments or timeout. |

## Execution order

After the first boot into installed Windows, Foundry runs the following before OOBE. Only tasks required by the deployment configuration are included.

| Order | Task |
| --- | --- |
| 1 | Install deferred driver packages, such as supported Lenovo EXE and Surface MSI packages. Drivers injected offline are already installed during deployment. |
| 2 | Import configured certificates and wired/Wi-Fi profiles. |
| 3 | Remove selected Copilot and AI Hub application packages. Other AI settings may already have been applied during deployment. |
| 4 | Remove the selected provisioned AppX packages. |
| 5 | Attempt OEM activation when eligible. This is skipped for custom answer files and volume-licensed deployments. |
| 6 | Run your enabled custom actions in the order shown, including any Restart Windows actions. |
| 7 | Perform cleanup of temporary deployment content, retaining execution results and logs. |

Required restarts can occur during this sequence. A planned restart resumes the remaining work; any deferred restart is handled before final cleanup. The interactive Autopilot assistant remains separate and appears during OOBE when configured.

## Prepare content

Import a complete folder when the installer needs transforms, CAB files, configuration, scripts or other binaries. Preserve their relative paths. A single-file import includes only that file.

For example:

```text
ExampleApplication/
  ExampleApplication.msi
  Organization.mst
  Data1.cab
```

Create a **Software (.exe/.msi)** action, import that folder, choose `ExampleApplication.msi`, verify the detected **MSI** type, and add the property `TRANSFORMS="Organization.mst"` if that transform is supported by the package. Keep the working directory empty to use the content root. Supply any required silent installation and no-restart options yourself, for example `/qn /norestart` for an MSI. Foundry does not add or enforce these switches. Enable **Generate installation log** to append `/l*v "{LogRoot}\ExampleApplication.log"`. This option is off by default and available only for MSI installers; EXE logging arguments depend on the vendor.

An example sequence is:

1. A PowerShell action that performs your prerequisite configuration.
2. The application action above.
3. A Restart action.
4. A Command line action that performs your final machine configuration.

Packages must be suitable for unattended machine installation. EXE silent and no-reboot options depend on the vendor; Foundry cannot infer them. Installers must wait for their work to complete and return a meaningful exit code. Do not launch an independent background installer and immediately report success.

You are responsible for choosing scripts and installers compatible with the target Windows architecture. Choose content suitable for every device that will use this configuration.

Actions using identical imported content share a working copy on the target. File changes made by an earlier action remain visible to later actions, including after planned restarts. They do not change the authoring cache.

Content is a temporary deployment source. Foundry removes its owned working copies at the end. If the application requires original media for repair, modification or later updates, arrange a durable source as part of your packaging. Do not rely on Foundry's Windows Temp folder, a removed USB drive, or the Windows Installer cache to preserve every required source file. Verify repair and servicing after Foundry cleanup.

## Arguments and installer logs

Review **Command preview** before saving. Foundry preserves your arguments without adding quiet mode, profile suppression, execution-policy overrides or restart suppression. You are responsible for valid arguments, unattended execution and preventing installer-owned restarts.

- **PowerShell arguments** go before `-File "<script>"`; **Script arguments** go after it. For example, enter `-NoProfile -ExecutionPolicy Bypass` in the first field only if your script requires those host options.
- **Command line** supplies the complete command to `cmd.exe /c`. Include its arguments in the same field.
- **Software (.exe/.msi)** appends your arguments to the selected executable or `msiexec.exe /i "<installer>"`.

For **Command line**, **Package content (optional)** accepts any file type needed by that command. For example, import `settings.reg` and enter `reg import settings.reg`. Importing content alone does not run it. Importing a single `.cmd` or `.bat` file fills an empty command field with the filename, without adding quotes; an existing command is never overwritten. Review the command and add any required arguments or quoting before saving. Leave **Working directory** empty to run from the imported content folder.

Commands and argument fields must each contain a single line. Use a script file for multiple commands. `{ContentRoot}` and `{LogRoot}` in the preview stand for paths selected during deployment; they are not variables that Foundry expands in your arguments. Use paths relative to the configured working directory when referencing imported files.

**Generate installation log** adds MSI verbose logging after your arguments. The filename is the selected installer basename with a `.log` extension. Each action has its own log folder, so two actions using the same installer do not overwrite each other's log. Leave the checkbox off if you supply your own MSI logging options. Foundry continues recording process output and execution results regardless of this checkbox. Captured output is stored separately under each action folder at `Process\output.log` so an installer filename cannot collide with it. PowerShell and Command line actions do not receive installer-logging switches.

## Execution and error policy

Actions run as SYSTEM during Windows setup. There is no signed-in user profile, guaranteed mapped drive or interactive desktop. Scripts requiring PowerShell 7 must arrange their own supported execution environment; Foundry uses Windows PowerShell 5.1 for PowerShell actions.

The default timeout is 1,800 seconds; the supported range is 1–86,400 seconds. Success defaults to exit code `0`. Applications also recognize `3010` as success requiring a controlled restart. Scripts and commands recognize additional restart codes only when you configure them. Success and restart lists must not overlap.

PowerShell scripts must return a failure explicitly when appropriate. Non-terminating PowerShell errors and failed native commands do not always become a failed process exit code automatically.

**Continue on error** is disabled by default: a failure stops later actions and prevents a successful handoff to OOBE. Enabling it permits later actions after an ordinary, fully observed failure, recording the sequence as completed with errors. It does not allow execution to continue when Foundry cannot determine whether an action finished safely, or after an installer-owned restart.

Keep secrets out of command arguments and normal output. Profile encryption does not make arbitrary script output or installer logs safe to share.

## Restarts and interrupted deployments

Use a Restart action or an installer restart-required code. Suppress installer-owned restarts. Foundry saves progress and resumes the remaining work after Windows restarts.

By default, a recognized restart-required result triggers a restart before the next action. **Defer restart** waits until the next explicit Restart or the end of the sequence. An explicit Restart runs even when no installer has requested one.

For an explicit Restart action, **Restart delay (seconds)** accepts `0` to `86400`. The default `0` adds no delay. A positive value shows a live countdown after progress has been saved. This setting does not change restarts requested by installers or built-in tasks.

An installer returning `1641` has initiated a restart outside this protocol. Foundry treats that as an uncertain execution, even though Windows Installer defines it as an installation-success code.

After a planned restart, Foundry resumes from its saved progress. If power is lost while an action is running, Foundry does not automatically retry that action. Missing or damaged execution records also stop the sequence. Inspect the results and logs before deciding whether to redeploy; scripts and installers are not necessarily safe to repeat.

If an interrupted process might still be using files, Foundry retains them and reports cleanup as pending. This does not retry the interrupted action.

## Follow progress in Windows Setup

The **Foundry Post-installation** console shows built-in and custom actions in execution order, with their status and elapsed time. Running actions appear in cyan, successful actions in green, failures in red, and waiting or skipped actions in gray. Warnings and restart countdowns appear in yellow. Text labels remain available when color or in-place updates are unavailable.

An action's elapsed time covers its start through completion, including a planned restart within that action.

<figure>
  <img src="../../.gitbook/assets/shared-post-installation-01-console-progress.png" alt="Foundry Post-installation console running Google Chrome as action 3 of 4, with action statuses, elapsed times and a log path">
  <figcaption>Follow the current action, completed results and elapsed times in the Post-installation console. Use the displayed log path to investigate an action.</figcaption>
</figure>

Like Bootstrap, the console uses English messages. Your custom action names appear as entered; the Foundry OSD configuration page remains translated. Script output and full command lines are not displayed in the progress screen; use the action logs for troubleshooting.

After a planned restart, the console restores completed results and indicates that execution is resuming. After success or completion with warnings, the final results remain visible for 10 seconds with a **Continuing Windows Setup** countdown. Windows Setup then continues and manages any remaining setup restarts. This final pause is separate from an explicit Restart action's delay.

## Cache, profiles and deployment media

Scripts and packages are stored in the local library under `%LOCALAPPDATA%\Foundry\Packages\PreOobe`, outside `boot.wim`. [Profiles](../deployment-profiles.md) contain action settings and content references, not package binaries. On another authoring PC, import the identical files and folder structure to restore those references.

USB and ISO media carry required content outside `boot.wim`, under `Cache\PreOobe`. Keep the complete generated media available until deployment finishes. A PXE boot image alone does not include these packages; see [PXE deployment](../media/pxe-deployment.md#post-installation-content).

[Bootstrap](../../reference/bootstrap.md#postinstall-preparation) downloads and verifies PostInstall at boot, independently of Deploy, before opening the deployment wizard. Standard release media needs network access for this preparation; a previously downloaded runtime cache still requires online verification. Debug media includes the locally prepared runtime. Your application packages must still be available from the deployment media. Missing or invalid required content blocks deployment before disk preparation.

After staging finishes, execution uses local files below `%SystemRoot%\Temp\Foundry`; it no longer needs the source USB/ISO. Your own scripts may still require a network or another resource.

## Custom answer files

Whenever selected built-in tasks or enabled custom actions require Foundry.PostInstall, Foundry automatically adds its required launch command to the deployment copy of a [custom unattend file](unattend.md). Imported originals remain unchanged. No additional authoring option or manual hook is required.

Existing custom commands, their order and unrelated settings are preserved. Detected integration conflicts block deployment instead of overriding your settings. Native Foundry settings receive the required integration automatically too.

After changing an imported answer file, use **Refresh source** on the Unattend page before rebuilding media. When no selected task requires PostInstall, this integration leaves the deployment copy unchanged.

Custom unattend is an expert configuration. Foundry validates its own integration prerequisites; you remain responsible for the custom Windows settings and commands you supply and for testing the complete deployment. Those settings and commands can interfere with PostInstall, OOBE or Autopilot even when no detectable conflict is present.

## Verify and troubleshoot

Successful WinPE staging is not proof of successful post-installation. Follow [Verify deployment](../../foundry-deploy/verify-deployment.md) through first boot, any planned restarts and OOBE.

Preserve the operation journal/results under `%SystemRoot%\Temp\Foundry\State\PreOobe` and diagnostics under `%SystemRoot%\Temp\Foundry\Logs\PreOobe`. The runtime's application log is `Foundry.PostInstall.log`; action and installer logs provide additional context. Results distinguish full success, permitted action failures, interruption and cleanup still pending.

Use [sanitized support exports](../../troubleshooting/logs-and-support.md) for the application's supported log sources. Post-installation results and target logs require separate collection. Review those files before sharing: raw script output and MSI logs may contain credentials or environment details, and arbitrary output cannot be guaranteed free of secrets.
