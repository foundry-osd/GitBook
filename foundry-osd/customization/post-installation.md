# Post-installation

{% hint style="info" %}
This page describes unreleased Foundry.PostInstall functionality. Use matching Foundry OSD and runtime assets when evaluating it. Validate the complete deployment on representative Windows images and hardware before production use.
{% endhint %}

Use **Customization > Post-installation** to run your own scripts, commands and application installers after Windows installation and before OOBE. The separate [OOBE page](oobe.md) configures the Windows first-run experience.

Foundry runs its selected built-in tasks first: deferred driver provisioning, network and certificate import, AI/AppX removal and eligible OEM activation. Your enabled actions then run in the order shown, followed by Foundry cleanup. Built-in tasks cannot be moved or deleted from this list. The existing interactive Autopilot registration assistant remains separate.

Additional update workflows added on this page require your own scripts or packages; there is no automatic “install all updates” action. Existing Foundry driver and firmware provisioning remains available.

## Add and order actions

1. Enable the page using its header switch.
2. Select **Add action** in the CommandBar and choose PowerShell, CMD, Application or Restart.
3. Give the action a recognizable name. For scripts and applications, import a file or a folder containing all required files.
4. Select the script or installer relative to that content, then enter its arguments. For CMD, enter the command line; content is optional.
5. Review the working directory, timeout, success/restart return codes, error policy and architecture, then save.
6. Select a row and use **Move up**, **Move down**, **Edit action**, **Enable/Disable** or **Remove**. Removing an action does not delete shared cached content.
7. Resolve missing-content or validation messages before creating media.

Disabling this page disables custom actions. Selected built-in tasks can still require Foundry.PostInstall and its answer-file launch hook.

Edit an action and import its changed source again to use a new immutable content revision. Refresh checks the local references; it does not silently accept changes to an original source folder.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-post-installation-01-ordered-actions.png`
- **Capture:** Show the final Post-installation page with its CommandBar, a script, an application and a Restart action, using sanitized demonstration names.
{% endhint %}

| Action | Configuration |
| --- | --- |
| PowerShell | A `.ps1` file and optional sibling content, executed by Windows PowerShell 5.1 with no profile and no interactive prompts. |
| CMD | A command interpreted by `cmd.exe`; optionally import files it needs. |
| Application | An EXE with vendor-specific silent arguments, or an MSI using generated quiet/no-restart/logging options and your additional properties or transforms. |
| Restart | An explicit restart at this position, followed by resumption at the next action. No process arguments or timeout. |

## Prepare content

Import a complete folder when the installer needs transforms, CAB files, configuration, scripts or other binaries. Preserve their relative paths. A single-file import includes only that file.

For example:

```text
ExampleApplication/
  ExampleApplication.msi
  Organization.mst
  Data1.cab
```

Create an **Application** action, import that folder, choose `ExampleApplication.msi`, select **MSI**, and add the property `TRANSFORMS="Organization.mst"` if that transform is supported by the package. Keep the working directory empty to use the content root. Foundry supplies quiet installation, restart suppression and an MSI log. Do not add conflicting restart options.

An example sequence is:

1. A PowerShell action that performs your prerequisite configuration.
2. The application action above.
3. A Restart action.
4. A CMD action that performs your final machine configuration.

Packages must be suitable for unattended machine installation. EXE silent and no-reboot options depend on the vendor; Foundry cannot infer them. Installers must wait for their work to complete and return a meaningful exit code. Do not launch an independent background installer and immediately report success.

The target working copy is shared by actions referencing the same content hash. Changes made by an earlier action remain visible to later consumers, including after planned restarts. The source cache remains immutable.

Content is a temporary deployment source. Foundry removes its owned working copies at the end. If the application requires original media for repair, modification or later updates, arrange a durable source as part of your packaging. Do not rely on Foundry's Windows Temp folder, a removed USB drive, or the Windows Installer cache to preserve every required source file. Verify repair and servicing after Foundry cleanup.

## Execution and error policy

Actions run as SYSTEM during Windows setup. There is no signed-in user profile, guaranteed mapped drive or interactive desktop. Scripts requiring PowerShell 7 must arrange their own supported execution environment; Foundry uses Windows PowerShell 5.1 for PowerShell actions.

The default timeout is 1,800 seconds; the supported range is 1–86,400 seconds. Success defaults to exit code `0`. Applications also recognize `3010` as success requiring a controlled restart. Scripts and commands recognize additional restart codes only when you configure them. Success and restart lists must not overlap.

PowerShell scripts must return a failure explicitly when appropriate. Non-terminating PowerShell errors and failed native commands do not always become a failed process exit code automatically.

**Stop** is the default error policy. It stops later actions and prevents a successful handoff to OOBE. **Continue** permits later actions after an ordinary, fully observed failure, recording the sequence as completed with errors. Continue cannot override corrupt state, failed checkpoint publication, uncertain process termination or an installer-owned restart.

Keep secrets out of command arguments and normal output. Profile encryption does not make arbitrary script output or installer logs safe to share.

## Restarts and interrupted deployments

Use a Restart action or an installer restart-required code. Suppress installer-owned restarts. Foundry saves a durable checkpoint and asks Windows Setup to restart and invoke the runner again.

By default, a recognized restart-required result triggers a restart before the next action. The defer option waits until the next explicit Restart or the end of the sequence. An explicit Restart runs even when no installer has requested one.

An installer returning `1641` has initiated a restart outside this protocol. Foundry treats that as an uncertain execution, even though Windows Installer defines it as an installation-success code.

A later boot resumes a valid committed restart checkpoint. If power is lost while an action is running, before that checkpoint exists, Foundry does not automatically replay it. A missing or corrupt journal also blocks execution. Inspect the result and logs before deciding whether to redeploy; arbitrary scripts and installers are not necessarily safe to repeat.

Cleanup retains potentially in-use resources when process termination is uncertain and records cleanup as pending. Reconciliation does not rerun the interrupted action.

## Cache, profiles and deployment media

Scripts and packages are stored in the local library under `%LOCALAPPDATA%\Foundry\Packages\PreOobe`, outside `boot.wim`. [Profiles](../deployment-profiles.md) contain action settings and content references, not package binaries. On another authoring PC, import the identical files and folder structure to restore those references.

USB and ISO media carry required content in an external `Cache\PreOobe` tree. Media creation validates snapshots and publishes the generation manifest after its content. Bare PXE `boot.wim` delivery is insufficient; provide the supported companion cache media. This feature does not introduce an HTTP/SMB package distribution service or remove Foundry Connect's network requirements.

Deploy verifies the applicable content and the exact matching PostInstall runtime before target disk preparation. For a catalog image downloaded onto prepared target storage, final WIM metadata checks occur after erasure, before image application. An available custom or cached image can be inspected earlier.

Foundry.PostInstall is distributed in x64 and ARM64 ZIP assets. Deploy accepts the companion archive identified by its authenticated release descriptor, reuses a verified cache entry or downloads that exact archive. It does not independently select a newer runner. Only the bounded runner may use WinPE temporary storage when writable external cache is unavailable and capacity permits; application packages do not receive this fallback.

After staging finishes, execution uses local files below `%SystemRoot%\Temp\Foundry`; it no longer needs the source USB/ISO. Your own scripts may still require a network or another resource.

## Custom answer files

Native Foundry settings add the required launch hook automatically. With a [custom unattend file](unattend.md), explicitly enable **Integrate with custom answer files** to add it to the deployment copy. Imported originals remain unchanged. Review the integration summary before deployment.

Select a catalog file and target architecture, then choose **Preview** to inspect the actual Foundry command and its insertion order. The preview omits unrelated XML and secrets. In exact-copy mode it validates the existing manual hook. A changed source must be refreshed on the Unattend page before it can be previewed or packaged. Deployment records the source and derived SHA-256 hashes in `State\PreOobe\unattend-integration.json`.

Exact-copy mode remains available, but a deployment requiring PostInstall needs the supported manual launch hook already present. Missing, duplicate or incompatible hooks block integration. Foundry preserves unrelated commands and does not silently renumber them.

For manual integration, place this command in the `Microsoft-Windows-Deployment` component's `RunSynchronous` list in the `specialize` pass. Use the component architecture matching the image (`amd64` or `arm64`), declare the `wcm` namespace as shown, and choose a unique `Order` from 1 to 500 after your existing commands:

```xml
<RunSynchronousCommand xmlns="urn:schemas-microsoft-com:unattend"
    xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" wcm:action="add">
  <Order>1</Order>
  <Description>Foundry PostInstall</Description>
  <Path>%SystemRoot%\System32\cmd.exe /d /s /c ""%SystemRoot%\Temp\Foundry\Runtime\PreOobe\Launch.cmd""</Path>
  <WillReboot>OnRequest</WillReboot>
</RunSynchronousCommand>
```

This fragment is not a complete answer file. Keep the path and `WillReboot` value exactly as shown. Foundry stages the executable and wrapper; do not launch the executable directly or put this command in `SetupComplete.cmd`.

Custom unattend is an expert configuration. Validation checks Foundry's integration prerequisites, not every possible command or Windows setting. Your custom settings can interfere with PostInstall, OOBE or Autopilot. Test the complete combination.

## Verify and troubleshoot

Successful WinPE staging is not proof of successful post-installation. Follow [Verify deployment](../../foundry-deploy/verify-deployment.md) through first boot, any planned restarts and OOBE.

Preserve the operation journal/results under `%SystemRoot%\Temp\Foundry\State\PreOobe` and diagnostics under `%SystemRoot%\Temp\Foundry\Logs\PreOobe`. The runtime's application log is `Foundry.PostInstall.log`; action and installer logs provide additional context. Results distinguish full success, permitted action failures, interruption and cleanup still pending.

Use [sanitized support exports](../../troubleshooting/logs-and-support.md) for the application's supported log sources. Post-installation results and target logs require separate collection. Review those files before sharing: raw script output and MSI logs may contain credentials or environment details, and arbitrary output cannot be guaranteed free of secrets.
