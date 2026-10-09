# Logs and support information

Collect the logs before you restart the device, recreate the media or deploy again. In Windows PE the logs live in memory and disappear at the restart, and a new deployment erases the logs left on the target disk.

| Where you are | Do this |
| --- | --- |
| In Foundry Connect or Foundry Deploy, on the target device | [Export logs from Foundry Connect and Foundry Deploy](#export-logs-from-foundry-connect-and-foundry-deploy). If it fails, see [the export error](#export-failed) |
| In Foundry OSD, on the workstation | [Export diagnostics from Foundry OSD](#export-diagnostics-from-foundry-osd) |
| In Windows after the restart | Copy the folders listed in [Log locations](#log-locations) |
| In front of a device that no longer starts | [Collect logs from a device that does not start](#collect-logs-from-a-device-that-does-not-start) |

## Export logs from Foundry Connect and Foundry Deploy

Both applications have a **Tools** menu in their menu bar. It stays available on the error screen.

1. Keep the Foundry USB drive connected. If the device started from an ISO or over PXE, insert a USB flash drive.
2. Select **Tools > Export diagnostics...**.
3. Read the path shown in the **Diagnostics exported** window. The archive is named `FoundrySupport-Foundry.Deploy-<date and time>.zip` or `FoundrySupport-Foundry.Connect-<date and time>.zip`.

There is no folder to choose in Windows PE. Foundry writes the archive to the root of the **Foundry Cache** volume of the USB drive, or, without one, to the first removable volume it finds.

| Menu item | Application | What it does |
| --- | --- | --- |
| **Open log file** | Foundry Deploy | Opens the current log in Notepad. Also available in the **Error details** window |
| **Refresh status** | Foundry Connect | Checks the network again. It does not export anything |
| **Export diagnostics...** | Both | Creates an archive in which the secrets Foundry recognizes are masked |
| **Export raw diagnostics...** | Both | Creates the same archive without masking, after a confirmation |

The Foundry Deploy archive contains its own log, the logs written by Windows PE startup and Foundry Connect during the same session, and, once the disk is prepared, the deployment logs and state files stored on the target disk. The Foundry Connect archive contains the logs of the Windows PE session. A file named `credentials.bin` is never included.

Use **Export raw diagnostics...** only when a support contact you trust asks for it. The confirmation says why: "Raw logs may contain credentials, identifiers, paths, network names, and other sensitive data."

## "Diagnostics could not be exported. Check the log for details." <a href="#export-failed" id="export-failed"></a>

**Where:** the **Diagnostics export failed** window, after **Export diagnostics...** or **Export raw diagnostics...**.

**Cause:** Foundry found no place to write the archive. The device started from an ISO or over PXE and has no USB flash drive, or the USB drive is full or write-protected. A USB hard disk does not count unless it carries a **Foundry Cache** volume.

**Fix:**

1. Insert a USB flash drive with free space and wait a few seconds.
2. Select **Tools > Export diagnostics...** again.

**Collect:** if the export still fails, use **Tools > Open log file** in Foundry Deploy and photograph the last lines.

## Log locations

`X:` is the Windows PE memory disk. `<cache>` is the **Foundry Cache** volume of a Foundry USB drive. `<target>` is the Windows partition of the target disk as seen from Windows PE; in the installed Windows it is `C:`.

| Written by | Location | Files |
| --- | --- | --- |
| Foundry OSD | Workstation: `%ProgramData%\Foundry\Logs`, or `%LOCALAPPDATA%\Foundry\Logs` when the first cannot be written | `Foundry.log` |
| Foundry OSD, ADK installation | Workstation: `%ProgramData%\Foundry\Logs\Adk` | One `.log` per setup stage |
| Windows PE startup | Windows PE: `X:\Foundry\Logs` | `FoundryBootstrap.log`, `FoundryBootstrap.Launcher.log` |
| Foundry Connect | Windows PE: `X:\Foundry\Logs` | `FoundryConnect.log` |
| Foundry Deploy | Windows PE: `X:\Foundry\Logs` | `FoundryDeploy.log` |
| Windows PE session copy | USB drive: `<cache>:\Logs\<session-id>` | Copies of the Windows PE logs, and a `Startup` folder |
| Deployment in progress | Target disk: `<target>:\Foundry\Logs\Deployment` and `<target>:\Foundry\State\Deployment` | Deployment logs, `deployment-state.json` |
| Deployment finished | Installed Windows: `C:\Windows\Temp\Foundry\Logs\Deployment` and `C:\Windows\Temp\Foundry\State\Deployment` | Deployment logs, `deployment-summary.json` |
| Post-installation | Installed Windows: `C:\Windows\Temp\Foundry\Logs\PreOobe` and `C:\Windows\Temp\Foundry\State\PreOobe` | `Foundry.PostInstall.log`, one folder per action, `execution-result.json` |
| Autopilot hardware hash upload | Installed Windows: `C:\Windows\Temp\Foundry\Logs\AutopilotHash` | Upload status and result files |
| Interactive Autopilot window | Installed Windows: `C:\Windows\Temp\Foundry\Logs\AutopilotRegistration` | Registration logs |

Things to know:

- Each log keeps a few older files next to it. Collect those that cover the time of the failure.
- The copy on the USB drive is made when the drive has a cache volume and is not guaranteed to be complete. Check that the files are there.
- The deployment logs move from `<target>:\Foundry` to `<target>:\Windows\Temp\Foundry` at the end of the deployment, and also after a failure or a cancellation once the disk has been prepared. If Foundry reports that evidence was retained at another path, collect that path too.
- `deployment-summary.json` lists every step with its result and the reason of each skipped step.
- For a failure before the first Foundry window, see [Windows PE startup troubleshooting](windows-pe-startup.md).

## Export diagnostics from Foundry OSD

1. In Foundry OSD, open **Settings > General**.
2. On **Export diagnostics**, select **Export...** and choose a folder.
3. Send the archive `FoundrySupport-Foundry.OSD-<date and time>.zip`.

The archive contains the Foundry OSD logs with recognized secrets masked. The original logs are not changed. It does not contain the ADK installation logs or anything from a target device: add those yourself when they matter.

**Advanced: export raw logs...** creates the same archive without masking. Use it only when a support contact you trust asks for it.

When **Enable remote diagnostics** is on, Foundry also sends application logs to the Foundry project. They do not replace the files above: attach local evidence to a support request. See [Telemetry and privacy](../reference/telemetry-and-privacy.md).

## Collect logs from a device that does not start

The logs of the last deployment stay on the target disk until the next deployment erases it.

1. Do not deploy again yet.
2. If the USB drive was connected during the failed deployment, look in `<cache>:\Logs` on another computer: the Windows PE session may have been copied there.
3. To read the target disk, connect it to another computer, or start the device from a Windows PE or recovery media that gives you a command prompt. Copy `Windows\Temp\Foundry\Logs` and `Windows\Temp\Foundry\State`, or `Foundry\Logs` and `Foundry\State` if the first folders do not exist.
4. Then deploy again.

## What to send

- The Foundry OSD version, and whether the media was recreated after the last Foundry OSD update.
- The application and the stage: Foundry OSD, Windows PE startup, Foundry Connect, Foundry Deploy, or the console after the restart.
- The exact message and, for Foundry Deploy, the "Failed step: ..." line.
- The device manufacturer and model, and the media type: USB drive, ISO or PXE.
- The Windows version, edition, language and architecture, and the driver source selected.
- The connection type: Ethernet or Wi-Fi, with or without 802.1X or a proxy.
- The Autopilot method or the Domain Join mode, when used.
- The exported archive, and whether the problem happens again on newly created media.

{% hint style="warning" %}
Read what you send. Masking covers only what Foundry recognizes. Remove passwords, tokens, certificates, hardware hashes, tenant identifiers, serial numbers and internal network names that remain, and never attach an answer file or a file named `credentials.bin`.
{% endhint %}

## Open a support issue

Search the [existing issues](https://github.com/foundry-osd/foundry/issues) first, then use the [bug report form](https://github.com/foundry-osd/foundry/issues/new?template=bug-report.yml). Describe the steps that lead to the problem, what you expected and what happened, and attach the items of [What to send](#what-to-send). Report a security vulnerability privately, as the repository's security policy explains, not in a public issue.

## Post-installation evidence

From the installed Windows, collect `C:\Windows\Temp\Foundry\Logs\PreOobe` and `C:\Windows\Temp\Foundry\State\PreOobe`. Script output and installer logs can contain secrets that masking does not catch: read them before sharing. What each file means is in [After the restart troubleshooting](after-the-restart.md).

## Domain Join evidence

From the installed Windows, collect `C:\Windows\Temp\Foundry\State\PreOobe\domain-join-result.json` and `C:\Windows\Temp\Foundry\Logs\PreOobe\Foundry.PostInstall.log`. The result file contains no account and no password. Never attach `credentials.bin`. How to read the result is in [Domain Join troubleshooting](domain-join.md).
