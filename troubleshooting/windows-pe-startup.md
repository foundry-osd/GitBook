# Windows PE startup troubleshooting

Use this page when the **Foundry Bootstrap** console stops, shows **Failed** in red, or shows yellow warnings before Foundry Deploy opens. [Windows PE startup](../foundry-connect/windows-pe-startup.md) describes a normal start.

The reason is the line under the list of stages. On a failure, the console also shows a `Session:` identifier and the `Log:` path. Logs are in `X:\Foundry\Logs`, which is lost when the device restarts: collect them first. See [Log locations](logs-and-support.md#log-locations).

| What you see | Go to section |
| --- | --- |
| Only "Foundry startup failed. See X:\Foundry\Logs for details." | [Foundry startup failed](#foundry-startup-failed) |
| "The boot environment could not be initialized." | [The boot environment could not be initialized](#boot-environment-not-initialized) |
| Yellow "Internet clock could not be resolved..." | [Internet clock could not be resolved](#internet-clock-not-resolved) |
| Yellow "Online runtime verification failed..." | [Online runtime verification failed](#online-verification-failed) |
| "Foundry Connect stopped with exit code 22." | [Exit code 22](#exit-code-22) |
| "Foundry Connect stopped with exit code 21." | [Exit code 21](#exit-code-21) |
| "Boot was cancelled. Deployment will not continue." | [Boot was cancelled](#boot-cancelled) |
| "This boot stage could not be completed. Check the session log for details." | [This boot stage could not be completed](#boot-stage-not-completed) |
| "Foundry Deploy stopped with exit code" and a number | [Foundry Deploy stopped](#foundry-deploy-stopped) |
| "Application readiness was not confirmed within two minutes..." | [Readiness was not confirmed](#readiness-not-confirmed) |
| "Application startup metadata is invalid..." | [Startup metadata is invalid](#startup-metadata-invalid) |
| Another yellow warning, or a last stage that shows **Unverified** | [Other warnings](#other-warnings) |

## "Foundry startup failed. See X:\Foundry\Logs for details." <a href="#foundry-startup-failed" id="foundry-startup-failed"></a>

- **Where:** A plain line at the command prompt, under the console.
- **Cause:** This line follows every failed startup. The reason is the line under the list of stages. If the console never appeared and this is the only line, Foundry Bootstrap itself could not start.
- **Fix:**
  1. Read the line under the list of stages and find it on this page.
  2. If there is no console, run `type X:\Foundry\Logs\FoundryBootstrap.Launcher.log` to read the exit code, then restart the device.
  3. If it happens again, recreate the media with the current version of Foundry OSD.
- **Collect:** `FoundryBootstrap.Launcher.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "The boot environment could not be initialized." <a href="#boot-environment-not-initialized" id="boot-environment-not-initialized"></a>

- **Where:** **Environment** shows **Failed**, a few seconds after the console appears.
- **Cause:** The startup met an error before its first stage. A known cause is two Foundry USB drives connected at the same time: each has a partition labelled **Foundry Cache**, and the startup refuses to choose between them. The log then contains "Multiple Foundry Cache volumes are connected. Disconnect the other deployment media and restart."
- **Fix:**
  1. Unplug every Foundry USB drive except the one the device started from.
  2. Restart the device.
  3. If only one drive was connected, collect `FoundryBootstrap.log`, which names the error, then recreate the media.
- **Collect:** `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Warning: Internet clock could not be resolved. Boot will continue without clock correction." <a href="#internet-clock-not-resolved" id="internet-clock-not-resolved"></a>

- **Where:** A yellow line under the elapsed time. **Environment** ends with **Done (warning)**.
- **Cause:** The device had no Internet access before Foundry Connect: no cable, DHCP not ready yet, or a network that requires authentication. This is expected on a Wi-Fi-only device.
- **Fix:** Nothing, when the last stage ends with **Ready**. The clock is read again at **Clock and time zone**, after Foundry Connect. If the time stays wrong, see [Wrong time or time zone](network.md#wrong-time-or-time-zone).
- **Collect:** Nothing.

## "Warning: Online runtime verification failed. Continuing with the original application provisioned on the boot media." <a href="#online-verification-failed" id="online-verification-failed"></a>

- **Where:** A yellow line under the elapsed time. **Network connection** ends with **Done (warning)**.
- **Cause:** Same as the previous warning: GitHub could not be reached before Foundry Connect, so the copy of Foundry Connect from the media is used.
- **Fix:** Nothing. Foundry Connect opens normally.
- **Collect:** Nothing.

## "Foundry Connect stopped with exit code 22." <a href="#exit-code-22" id="exit-code-22"></a>

- **Where:** **Network connection** shows **Failed**. Foundry Connect does not open, or closes at once.
- **Cause:** Foundry Connect could not read its configuration on the media: the configuration file is missing or damaged, or the Wi-Fi passphrase or the PFX password stored on the media cannot be decrypted.
- **Fix:** Recreate the media in Foundry OSD.
- **Collect:** `FoundryConnect.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Foundry Connect stopped with exit code 21." <a href="#exit-code-21" id="exit-code-21"></a>

- **Where:** **Network connection** shows **Failed**.
- **Cause:** Foundry Connect met an unexpected error while starting.
- **Fix:**
  1. Restart the device.
  2. If it happens again, recreate the media with the current version of Foundry OSD.
- **Collect:** `FoundryConnect.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Boot was cancelled. Deployment will not continue." <a href="#boot-cancelled" id="boot-cancelled"></a>

- **Where:** **Network connection** shows **Cancelled** and the second line of the console reads "Startup cancelled".
- **Cause:** The Foundry Connect window was closed before the startup continued, or Ctrl+C was pressed in the console. Closing Foundry Connect never skips the network check.
- **Fix:**
  1. Restart the device and start from the media again.
  2. Without a restart: if a command prompt remains in the console window, you may type `X:\Foundry\Bootstrap\Launch.cmd` and press Enter. This is the command the device runs at startup, but running it a second time is not confirmed on a device. Use it only when no Foundry Connect window is open: after Ctrl+C, Foundry Connect may still be running, so close it first or restart.
- **Collect:** Nothing.

## "This boot stage could not be completed. Check the session log for details." <a href="#boot-stage-not-completed" id="boot-stage-not-completed"></a>

- **Where:** Usually **Deployment files** shows **Failed**. Sometimes **Network connection**.
- **Cause:**
  1. GitHub does not answer from Windows PE. Every start asks `api.github.com` which release to use, even when Foundry Deploy is already on the USB drive. The GitHub download hosts are needed too when nothing is kept: always from an ISO, and the first time or after a new release from a USB drive. The log contains "No authenticated original Foundry.Deploy payload is available. Recreate the boot media or connect to the network to obtain a verified runtime.", or the same line for `Foundry.PostInstall` on media without Domain Join: neither application is on the media, as [What each media type carries](../foundry-osd/media/README.md#what-each-media-type-carries) explains.
  2. The device clock is wrong, and secure connections fail.
  3. Possibly, many devices start behind the same public IP address. Each start sends three or four anonymous requests to `api.github.com`, and GitHub can limit anonymous requests from one address. Foundry does not report this case separately: the log shows the same line as for cause 1.
  4. At **Network connection**: the copy of Foundry Connect on the media is missing or failed its integrity check. The log contains "No authenticated original Foundry.Connect payload is available." or "Runtime archive SHA256 does not match the trusted digest."
- **Fix:**
  1. Check that the network allows the hosts in [Network endpoints](../reference/network-endpoints.md). **Network ready** in Foundry Connect does not prove it.
  2. Check the date and time in the firmware of the device.
  3. Restart the device and try again.
  4. For a large batch behind one public address, space the starts or use a network with several public addresses.
  5. If the failure is at **Network connection**, recreate the media.
- **Collect:** `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Foundry Deploy stopped with exit code" and a number <a href="#foundry-deploy-stopped" id="foundry-deploy-stopped"></a>

- **Where:** **Deployment application** shows **Failed**.
- **Cause:** Foundry Deploy closed before its window was ready.
- **Fix:**
  1. Restart the device.
  2. If it happens again, recreate the media with the current version of Foundry OSD.
- **Collect:** `FoundryDeploy.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Application readiness was not confirmed within two minutes. The application may still be running." <a href="#readiness-not-confirmed" id="readiness-not-confirmed"></a>

- **Where:** **Network connection** or **Deployment application** shows **Failed**.
- **Cause:** Foundry Connect or Foundry Deploy did not report a usable window within 2 minutes. The console stops waiting but does not close the application.
- **Fix:**
  1. Look for the application window before doing anything else.
  2. If the Foundry Deploy window is open, the application is running: continue in it.
  3. If the stage was **Network connection**, restart the device: the startup no longer waits for Foundry Connect.
- **Collect:** `FoundryBootstrap.log` and the log of the application. See [Logs and support information](logs-and-support.md).

## "Application startup metadata is invalid. Recreate the boot media or refresh the runtime cache." <a href="#startup-metadata-invalid" id="startup-metadata-invalid"></a>

- **Where:** **Network connection** or **Deployment application** shows **Failed**.
- **Cause:** The startup description inside the package of Foundry Connect or Foundry Deploy is invalid.
- **Fix:**
  1. Recreate the media with the current version of Foundry OSD.
  2. If it happens again, open a support issue with the log.
- **Collect:** `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## Other warnings <a href="#other-warnings" id="other-warnings"></a>

Yellow lines that start with "Warning:" never stop the startup. The console shows three of them at most, then a line such as "2 more warnings are available in the log."

<details>

<summary>Startup warnings</summary>

| Warning | Meaning | What to do |
| --- | --- | --- |
| "Wired AutoConfig service could not be started. Boot will continue." | The Windows PE service for wired 802.1X did not start. A plain wired network still works. | Restart. A wired 802.1X profile cannot authenticate without it. |
| "Wi-Fi AutoConfig service could not be started. Boot will continue." | The Windows PE wireless service did not start. Foundry Connect shows no Wi-Fi list. | Restart. See [The Wi-Fi list is missing](network.md#wi-fi-list-missing). |
| "System clock could not be corrected. Boot will continue." | The Internet time was read but the clock could not be set. | Set the date and time in the firmware. |
| "Public IP timezone could not be resolved. Boot will continue with UTC." | Automatic time zone detection got no answer. | See [Wrong time or time zone](network.md#wrong-time-or-time-zone). |
| "IANA timezone map could not be loaded. Boot will continue with UTC if needed." | A file used to convert the detected time zone could not be read. | Nothing. The time zone may stay on UTC. |
| "WinPE timezone could not be updated. Boot will continue." | The time zone was found but could not be applied. | Nothing. Times in Windows PE stay in the previous zone. |
| "Embedded deployment timezone configuration could not be read. Boot will continue with automatic detection." | The time zone setting on the media could not be read. | Recreate the media if it repeats. |
| "The verified runtime could not be saved to the cache. This boot can continue." | The downloaded application could not be written to the USB drive. | Nothing for this start. If it repeats, check the USB drive. |
| "Foundry Connect could not be updated. Continuing." | On a USB drive, the check for a newer Foundry Connect failed. | Nothing. |
| "This application does not support startup confirmation. Readiness will remain unverified." | The console cannot confirm that the application opened. The last stage shows **Unverified** and the second line reads "Deployment application launched". | Check that the Foundry Deploy window is open. |
| "Some network preparation was unavailable. Continuing.", "Early clock synchronization was unavailable. Continuing to Foundry Connect." or "Some system preparation was unavailable. Continuing." | A preparation step met an unexpected error. | Nothing, unless a later stage fails. |

</details>
