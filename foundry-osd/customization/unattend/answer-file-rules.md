# Answer file rules

This page supports [Unattend (custom answer files)](../unattend.md). It lists what Foundry checks in a custom answer file, where it adds its own command, which settings block Windows Autopilot, and what each source check message means.

## When each rule is checked

| Rule | Checked by | When |
| --- | --- | --- |
| [Format of the file](#rules-checked-at-import) | Foundry OSD | When you import the file |
| Architecture: at least one component must apply to the Windows image | Foundry Deploy | On **Target device**, once Windows is chosen |
| [Room for Foundry's command](#when-foundry-adds-its-command) | Foundry Deploy | In the **Validate answer file** step, before the disk is erased |
| [Settings that block Windows Autopilot](#settings-that-block-windows-autopilot) | Foundry OSD warns at import; Foundry Deploy refuses the file | On **Target device** |

## Rules checked at import

Foundry checks the format of a file when you import it, and its architecture on **Target device** in Foundry Deploy, once Windows is chosen: at least one component must apply to that Windows image.

| Rule | Detail |
| --- | --- |
| Size and format | Valid Windows answer-file XML of at most 4 MiB, without DTD or external entity. |
| Passes | Only `specialize` and `oobeSystem`. A non-empty `windowsPE`, `offlineServicing`, `generalize`, `auditSystem` or `auditUser` pass, a non-empty `servicing` element, or a reseal to audit mode is refused. Foundry removes nothing for you. |
| Components | At least one component with settings. `processorArchitecture` is `amd64`, `arm64`, `x86`, `arm`, `neutral` or `*`. |

## When Foundry adds its command <a href="#when-foundry-adds-its-command" id="when-foundry-adds-its-command"></a>

Foundry adds one command to the deployment copy whenever the deployment has work to do after the restart: Post-installation actions, AppX or AI component removals, a driver pack installed after the restart, network profiles copied to Windows, or Domain Join.

The command goes at the end of the `RunSynchronous` list of the `Microsoft-Windows-Deployment` component in the `specialize` pass, with an `Order` one more than the highest in that list. Your `specialize` commands therefore run before Foundry's work and your `oobeSystem` commands after it. The file must have:

- at most one `<settings pass="specialize">` block, not marked `wasPassProcessed`;
- in that block, at most one `Microsoft-Windows-Deployment` component, whose `processorArchitecture` is exactly that of the deployed Windows (`amd64` or `arm64`);
- at most one `RunSynchronous` list in that component, where every `Order` is a different whole number from 1 to 500 and the highest is below 500;
- no command of your own that is described as `Foundry PostInstall` or that calls `\Runtime\PreOobe\Launch.cmd`.

These rules are checked only in Foundry Deploy, in the **Validate answer file** step, before the disk is erased. A file that repeats `Microsoft-Windows-Deployment` for `amd64` and `arm64` is imported without error and refused there. The messages of that step are in [Answer file validation](../../../troubleshooting/deployment/checks-and-image-download.md#validate-answer-file).

With a [custom Windows image](../custom-windows-images.md) that already contains an answer file under `Windows\Panther\Unattend`, a deployment that needs Foundry's command stops after the disk is erased. Remove that file from the image before you capture it.

## Settings that block Windows Autopilot <a href="#settings-that-block-windows-autopilot" id="settings-that-block-windows-autopilot"></a>

With Windows Autopilot in **JSON profile** or **Interactive** mode, Foundry Deploy refuses a file that contains any of these. Foundry OSD only warns at import.

- `AutoLogon` with `Enabled` set to true in `Microsoft-Windows-Shell-Setup`.
- In the `oobeSystem` pass of `Microsoft-Windows-Shell-Setup`: any `LocalAccount` under `UserAccounts`, or `SkipMachineOOBE`, `SkipUserOOBE`, `HideOnlineAccountScreens` or `HideLocalAccountScreen` set to true under `OOBE`.
- In the `specialize` pass of `Microsoft-Windows-UnattendedJoin`: a `JoinDomain` value under `Identification`, or `AccountData` under `Identification/Provisioning`.

The message Foundry Deploy shows is in [A message under Answer file](../../../troubleshooting/deployment/before-deployment-starts.md#answer-file-rejected).

## Source check messages

Foundry OSD shows the first message on the Unattend page and the others on the line under a file of the **Answer files** list.

| Message | What to do |
| --- | --- |
| "Some sources are missing, changed, or invalid. Refresh or remove these entries before creating media." | Read the line under each file in the list: it carries one of the messages below. |
| "The source changed. Use Refresh source to accept the updated file before creating media." | Select the file, then **Refresh source**. |
| "The source could not be read. Check that the file still exists and is accessible." | Restore the file or the access to its folder, then **Check sources**, or remove the entry. |
| "Source validation timed out. Check access to the source location and try again." | The source did not answer within 15 seconds, for example on a network share. Restore access, then **Check sources**. |
| "Two source checks are still waiting for file access. Restore the unavailable source locations, then check sources again." | Two earlier checks are still blocked on an unreachable location. Restore access or remove those entries. |
| "This source now duplicates another imported file. Remove this entry and choose the existing file instead." | After a refresh, two entries have the same content. Remove one. |

## Related

- [Unattend (custom answer files)](../unattend.md)
- [What a custom answer file overrides](../unattend.md#what-a-custom-answer-file-overrides)
- [Windows deployment troubleshooting](../../../troubleshooting/deployment.md)
