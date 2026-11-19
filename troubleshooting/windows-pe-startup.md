# Windows PE startup troubleshooting

Use this page when the **Foundry WinPE** console stops, shows **Failed** in red, shows yellow warnings, or a **Foundry stopped** page appears. [Windows PE startup](../foundry-connect/windows-pe-startup.md) describes a normal start.

The reason is the line under the list of stages. On a failure, the console also shows a `Session:` identifier and the `Log:` path. Logs are in `X:\Foundry\Logs`, which is lost when the device restarts: collect them first. See [Log locations](logs-and-support.md#log-locations).

| What you see | Go to section |
| --- | --- |
| Only "Foundry stopped with an error. See X:\Foundry\Logs for details." | [Foundry stopped with an error](#foundry-startup-failed) |
| "The boot environment could not be initialized." | [The boot environment could not be initialized](#boot-environment-not-initialized) |
| Yellow "Internet clock could not be resolved..." | [Internet clock could not be resolved](#internet-clock-not-resolved) |
| "This boot media is obsolete. Update Foundry OSD and recreate the media." on a full-screen page | [This boot media is obsolete](#boot-media-obsolete) |
| "Foundry Connect could not load its configuration. Recreate the boot media." | [Foundry Connect could not load its configuration](#exit-code-22) |
| "Foundry Connect could not start. Check the session log for details." | [Foundry Connect could not start](#exit-code-21) |
| "Foundry Connect stopped unexpectedly. Check the session log for details." | [Foundry Connect stopped unexpectedly](#exit-code-23) |
| "Boot was cancelled. Deployment will not continue." | [Boot was cancelled](#boot-cancelled) |
| "This boot stage could not be completed. Check the session log for details." | [This boot stage could not be completed](#boot-stage-not-completed) |
| A page titled **Foundry stopped**, and a yellow "Foundry stopped unexpectedly with exit code" and a number in the console | [Foundry stopped](#foundry-stopped) |
| "Foundry Deploy could not start..." or "Foundry Deploy could not load its configuration..." | [Foundry Deploy stopped](#foundry-deploy-stopped) |
| "Application readiness was not confirmed within two minutes..." | [Readiness was not confirmed](#readiness-not-confirmed) |
| "Application startup metadata is invalid..." | [Startup metadata is invalid](#startup-metadata-invalid) |
| Another yellow warning | [Other warnings](#other-warnings) |

## "Foundry stopped with an error. See X:\Foundry\Logs for details." <a href="#foundry-startup-failed" id="foundry-startup-failed"></a>

- **Where:** A plain line at the command prompt, under the console.
- **Cause:** This line follows every failed run, whether Foundry could not start or stopped later. The reason is the line under the list of stages. If the console never appeared and this is the only line, Foundry WinPE itself could not start.
- **Fix:**
  1. Read the line under the list of stages and find it on this page.
  2. If there is no console, run `type X:\Foundry\Logs\FoundryBootstrap.Launcher.log` to read the exit code, then restart the device.
  3. If it happens again, recreate the media with the current version of Foundry OSD.
- **Collect:** `FoundryBootstrap.Launcher.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "The boot environment could not be initialized." <a href="#boot-environment-not-initialized" id="boot-environment-not-initialized"></a>

- **Where:** **Environment** shows **Failed**, a few seconds after the console appears.
- **Cause:** The startup met an error before its first stage. A known cause is two Foundry USB drives connected at the same time: each has a partition labeled **Foundry Cache**, and the startup refuses to choose between them. The log then contains "Multiple Foundry Cache volumes are connected. Disconnect the other deployment media and restart."
- **Fix:**
  1. Unplug every Foundry USB drive except the one the device started from.
  2. Restart the device.
  3. If only one drive was connected, collect `FoundryBootstrap.log`, which names the error, then recreate the media.
- **Collect:** `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Warning: Internet clock could not be resolved. Boot will continue without clock correction." <a href="#internet-clock-not-resolved" id="internet-clock-not-resolved"></a>

- **Where:** A yellow line under the elapsed time. **Environment** ends with **Done (warning)**.
- **Cause:** The device had no Internet access before Foundry Connect: no cable, DHCP not ready yet, or a network that requires authentication. This is expected on a Wi-Fi-only device.
- **Fix:** Nothing: the clock is read again after Foundry Connect, when the network works. If the time stays wrong, the device keeps its firmware clock: see [Wrong time or time zone](network.md#wrong-time-or-time-zone).
- **Collect:** Nothing.

## "This boot media is obsolete. Update Foundry OSD and recreate the media." <a href="#boot-media-obsolete" id="boot-media-obsolete"></a>

- **Where:** A full-screen page with a **Close** button, shown where Foundry Connect should open. Nothing else in it can be used, and no deployment starts.
- **Cause:** The media was created with Foundry OSD v26.10.18.1 or earlier. Those media download Foundry Connect and Foundry Deploy from the latest release at each start, and the latest release now only shows this page.
- **Fix:**
  1. Update Foundry OSD.
  2. Recreate the ISO or the PXE files, or [update the USB drive](../foundry-osd/media/update-usb.md).
  3. Start the device from the new media.
- **Good to know:** Such media that cannot reach GitHub (no network, or GitHub limits requests from your address) do not show the page: they start the application stored on the media and deploy as before. Recreate them anyway to get the current version.
- **Collect:** Nothing.

## "Foundry Connect could not load its configuration. Recreate the boot media." <a href="#exit-code-22" id="exit-code-22"></a>

- **Where:** **Foundry** shows **Failed**. Foundry Connect does not open, or closes at once.
- **Cause:** The configuration file of Foundry Connect on the media is missing or damaged. A Wi-Fi or 802.1X secret that cannot be read does not stop Foundry Connect: only that network is marked unavailable, see [Provisioned Wi-Fi "Unavailable"](network.md#provisioned-wifi-unavailable).
- **Fix:** Recreate the media in Foundry OSD.
- **Collect:** `FoundryConnect.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Foundry Connect could not start. Check the session log for details." <a href="#exit-code-21" id="exit-code-21"></a>

- **Where:** **Foundry** shows **Failed**.
- **Cause:** Foundry Connect met an unexpected error while starting.
- **Fix:**
  1. Restart the device.
  2. If it happens again, recreate the media with the current version of Foundry OSD.
- **Collect:** `FoundryConnect.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Foundry Connect stopped unexpectedly. Check the session log for details." <a href="#exit-code-23" id="exit-code-23"></a>

- **Where:** **Foundry** shows **Failed** after Foundry Connect had opened.
- **Cause:** Foundry Connect met an unexpected error while it was running.
- **Fix:**
  1. Restart the device.
  2. If it happens again, recreate the media with the current version of Foundry OSD.
- **Collect:** `FoundryConnect.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Boot was cancelled. Deployment will not continue." <a href="#boot-cancelled" id="boot-cancelled"></a>

- **Where:** **Foundry** shows **Cancelled** and the second line of the console reads "Startup cancelled".
- **Cause:** One of these:
  - The Foundry window was closed during Foundry Connect, before the startup continued. Closing the window during Foundry Connect never skips the network check, and Foundry Deploy does not open.
  - Foundry Deploy closed before its wizard opened: the deployment password prompt was cancelled, or the network cache window was closed.
  - Ctrl+C was pressed in the console.
- **Fix:**
  1. Restart the device and start from the media again.
  2. Without a restart: if a command prompt remains in the console window, you may type `X:\Foundry\WinPE\Launch.cmd` and press Enter. This is the command the device runs at startup, but running it a second time is not confirmed on a device. Use it only when no Foundry window is open: after Ctrl+C, Foundry may still be running, so close it first or restart.
- **Collect:** Nothing.

## "This boot stage could not be completed. Check the session log for details." <a href="#boot-stage-not-completed" id="boot-stage-not-completed"></a>

- **Where:** A stage shows **Failed**.
- **Cause:** The stage met an unexpected error. Nothing is downloaded at startup, so the network is not the cause.
- **Fix:**
  1. Restart the device and try again.
  2. If it happens again, recreate the ISO or the PXE files, or [update the USB drive](../foundry-osd/media/update-usb.md), with the current version of Foundry OSD.
- **Collect:** `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## A page titled "Foundry stopped" <a href="#foundry-stopped" id="foundry-stopped"></a>

- **Where:** A full-screen page in front of the console, after the Foundry window had appeared. The console shows a yellow "Foundry stopped unexpectedly with exit code" and a number. The page shows the folder that holds the logs, in the language you chose in Foundry Connect, or else the language of Windows PE.
- **Cause:** The Foundry application closed unexpectedly, after its first window appeared: it crashed, or it was ended, for example from Task Manager. A restart or a shutdown of Windows PE, as when Foundry Deploy restarts the device at the end of a deployment, is never reported this way.
- **Fix:**
  1. Read the message on the page.
  2. If **Relaunch** is offered, the deployment had not started to prepare the target disk: select it. Foundry starts again from Foundry Connect, and the target disk was not changed.
  3. If only **Close** is offered, the deployment may already have changed the target disk, so Foundry cannot be relaunched. Select **Close** (or press Escape), then restart the device from the media to begin a new deployment.
  4. If it happens again, recreate the media with the current version of Foundry OSD and open a support issue with the logs.
- **Good to know:** Foundry is never relaunched by itself. A failure in Foundry Connect or Foundry Deploy that reports its own message, such as the ones above and below, does not show this page.
- **Collect:** `FoundryBootstrap.log`, `FoundryConnect.log` and `FoundryDeploy.log`. See [Logs and support information](logs-and-support.md).

## "Foundry Deploy could not start" or "could not load its configuration" <a href="#foundry-deploy-stopped" id="foundry-deploy-stopped"></a>

- **Where:** **Foundry** shows **Failed** after Foundry Connect, with one of these lines: "Foundry Deploy could not start. Check the session log for details." or "Foundry Deploy could not load its configuration. Recreate the boot media."
- **Cause:** Foundry Deploy could not read its configuration or start. This is a failure reported by Foundry Deploy itself, so no **Foundry stopped** page appears.
- **Fix:**
  1. Restart the device.
  2. If it happens again, recreate the media with the current version of Foundry OSD.
- **Collect:** `FoundryDeploy.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Application readiness was not confirmed within two minutes. The application may still be running." <a href="#readiness-not-confirmed" id="readiness-not-confirmed"></a>

- **Where:** **Foundry** shows **Failed**.
- **Cause:** Foundry did not show its window with Foundry Connect within 2 minutes. The console stops waiting but does not close the application.
- **Fix:**
  1. Look for the Foundry window before doing anything else.
  2. If a Foundry window is open, the application is running: continue in it.
  3. Otherwise restart the device: the startup no longer waits for Foundry.
- **Collect:** `FoundryBootstrap.log` and `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## "Application startup metadata is invalid. Recreate the boot media or use Update USB." <a href="#startup-metadata-invalid" id="startup-metadata-invalid"></a>

- **Where:** **Foundry** shows **Failed**, and no Foundry window opens.
- **Cause:** The startup description of the Foundry WinPE program on the media is missing or invalid.
- **Fix:**
  1. Recreate the media with the current version of Foundry OSD, or [update the USB drive](../foundry-osd/media/update-usb.md).
  2. If it happens again, open a support issue with the log.
- **Collect:** `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## Other warnings <a href="#other-warnings" id="other-warnings"></a>

Yellow lines that start with "Warning:" never stop the startup. The console shows three of them at most, then a line such as "2 more warnings are available in the log."

<details>

<summary>Startup warnings</summary>

| Warning | Meaning | What to do |
| --- | --- | --- |
| "Wired AutoConfig service could not be started. Boot will continue." | The Windows PE service for wired 802.1X did not start. A plain wired network still works. | Restart. A wired 802.1X profile cannot authenticate without it. |
| "Wi-Fi AutoConfig service could not be started. Boot will continue." | The Windows PE wireless service did not start. Foundry Connect shows no Wi-Fi card and nothing about Wi-Fi. | Restart. See [The Wi-Fi card is missing](network.md#wi-fi-list-missing). |
| "System clock could not be corrected. Boot will continue." | The Internet time was read but the clock could not be set. | Set the date and time in the firmware. |
| "Online runtime verification failed. Continuing with the original application provisioned on the boot media.", "The verified runtime could not be saved to the cache. This boot can continue." or "Foundry Connect could not be updated. Continuing." | The media was created with an older Foundry OSD, which still looks for newer Foundry applications at each start, and that lookup or its copy to the USB drive failed. | Nothing for this start. Recreate the ISO or the PXE files, or [update the USB drive](../foundry-osd/media/update-usb.md), with the current Foundry OSD. |
| "Some network preparation was unavailable. Continuing." or "Early clock synchronization was unavailable. Continuing to Foundry." | A preparation step met an unexpected error. | Nothing, unless a later stage fails. |

</details>

The time zone and the full clock synchronization run later, inside Foundry, after Foundry Connect: they never show a line in the console and only write to `FoundryConnect.log`. If the time zone is wrong, see [Wrong time or time zone](network.md#wrong-time-or-time-zone).
