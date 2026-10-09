# Windows PE startup troubleshooting

Use this page when the **Foundry Bootstrap** console stops, shows **Failed** in red, or shows yellow warnings before Foundry Deploy opens. [Windows PE startup](../foundry-connect/windows-pe-startup.md) describes a normal start.

The reason is the line under the list of stages. On a failure, the console also shows a `Session:` identifier and the `Log:` path. Logs are in `X:\Foundry\Logs`, which is lost when the device restarts: collect them first. See [Log locations](logs-and-support.md#log-locations).

| What you see | Go to section |
| --- | --- |
| Only "Foundry startup failed. See X:\Foundry\Logs for details." | [Foundry startup failed](#foundry-startup-failed) |
| "The boot environment could not be initialized." | [The boot environment could not be initialized](#the-boot-environment-could-not-be-initialized.) |
| Yellow "Internet clock could not be resolved..." | [Internet clock could not be resolved](#warning-internet-clock-could-not-be-resolved.-boot-will-continue-without-clock-correction.) |
| Yellow "Online runtime verification failed..." | [Online runtime verification failed](#warning-online-runtime-verification-failed.-continuing-with-the-original-application-provisioned-on-the-boot-media.) |
| "Foundry Connect stopped with exit code 22." | [Exit code 22](#foundry-connect-stopped-with-exit-code-22.) |
| "Foundry Connect stopped with exit code 21." | [Exit code 21](#foundry-connect-stopped-with-exit-code-21.) |
| "Boot was cancelled. Deployment will not continue." | [Boot was cancelled](#boot-was-cancelled.-deployment-will-not-continue.) |
| "This boot stage could not be completed. Check the session log for details." | [This boot stage could not be completed](#this-boot-stage-could-not-be-completed.-check-the-session-log-for-details.) |
| "Foundry Deploy stopped with exit code" and a number | [Foundry Deploy stopped](#foundry-deploy-stopped-with-exit-code-and-a-number) |
| "Application readiness was not confirmed within two minutes..." | [Readiness was not confirmed](#application-readiness-was-not-confirmed-within-two-minutes.-the-application-may-still-be-running.) |
| "Application startup metadata is invalid..." | [Startup metadata is invalid](#application-startup-metadata-is-invalid.-recreate-the-boot-media-or-refresh-the-runtime-cache.) |
| Another yellow warning, or a last stage that shows **Unverified** | [Other warnings](#other-warnings) |

## "Foundry startup failed. See X:\Foundry\Logs for details." <a href="#foundry-startup-failed" id="foundry-startup-failed"></a>

- **Where:** A plain line at the command prompt, under the console.
- **Cause:** This line follows every failed startup. The reason is the line under the list of stages. If the console never appeared and this is the only line, Foundry Bootstrap itself could not start.
- **Fix:**
  1. Read the line under the list of stages and find it on this page.
  2. If there is no console, run `type X:\Foundry\Logs\FoundryBootstrap.Launcher.log` to read the exit code, then restart the device.
  3. If it happens again, recreate the media with the current version of Foundry OSD.
- **Collect:** `FoundryBootstrap.Launcher.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "The boot environment could not be initialized."

- **Where:** **Environment** shows **Failed**, a few seconds after the console appears.
- **Cause:** Most often, two Foundry USB drives are connected. Each has a partition labelled **Foundry Cache**, and the startup refuses to choose between them. The log then contains "Multiple Foundry Cache volumes are connected. Disconnect the other deployment media and restart."
- **Fix:**
  1. Unplug every Foundry USB drive except the one the device started from.
  2. Restart the device.
  3. If only one drive was connected, recreate the media.
- **Collect:** `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Warning: Internet clock could not be resolved. Boot will continue without clock correction."

- **Where:** A yellow line under the elapsed time. **Environment** ends with **Done (warning)**.
- **Cause:** The device had no Internet access before Foundry Connect: no cable, DHCP not ready yet, or a network that requires authentication. This is expected on a Wi-Fi-only device.
- **Fix:** Nothing, when the last stage ends with **Ready**. The clock is set again at **Clock and time zone**, after Foundry Connect. If the warning is still the cause of a later failure, see [Wrong time or time zone](network.md#the-time-or-the-time-zone-is-wrong-in-windows-pe).
- **Collect:** Nothing.

## "Warning: Online runtime verification failed. Continuing with the original application provisioned on the boot media."

- **Where:** A yellow line under the elapsed time. **Network connection** ends with **Done (warning)**.
- **Cause:** Same as the previous warning: GitHub could not be reached before Foundry Connect, so the copy of Foundry Connect from the media is used.
- **Fix:** Nothing. Foundry Connect opens normally.
- **Collect:** Nothing.

## "Foundry Connect stopped with exit code 22."

- **Where:** **Network connection** shows **Failed**. Foundry Connect does not open, or closes at once.
- **Cause:** Foundry Connect could not read its configuration on the media: the configuration file is missing or damaged, or the Wi-Fi passphrase or the PFX password stored on the media cannot be decrypted.
- **Fix:** Recreate the media in Foundry OSD.
- **Collect:** `FoundryConnect.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Foundry Connect stopped with exit code 21."

- **Where:** **Network connection** shows **Failed**.
- **Cause:** Foundry Connect met an unexpected error while starting.
- **Fix:**
  1. Restart the device.
  2. If it happens again, recreate the media with the current version of Foundry OSD.
- **Collect:** `FoundryConnect.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Boot was cancelled. Deployment will not continue."

- **Where:** **Network connection** shows **Cancelled** and the second line of the console reads "Startup cancelled".
- **Cause:** The Foundry Connect window was closed before the startup continued, or Ctrl+C was pressed in the console. Closing Foundry Connect never skips the network check.
- **Fix:** Do one of the following.
  1. Restart the device and start from the media again.
  2. At the command prompt that remains in the console window, type `X:\Foundry\Bootstrap\Launch.cmd` and press Enter. The startup runs again from the first stage.
- **Collect:** Nothing.

## "This boot stage could not be completed. Check the session log for details."

- **Where:** Usually **Deployment files** shows **Failed**. Sometimes **Network connection**.
- **Cause:**
  1. GitHub is not reachable from Windows PE. Foundry Deploy is not on the media: it is downloaded at every start, so **Deployment files** needs `api.github.com` and the GitHub download hosts. The log contains "No authenticated original Foundry.Deploy payload is available. Recreate the boot media or connect to the network to obtain a verified runtime."
  2. The device clock is wrong, and secure connections fail.
  3. Many devices start from the same public IP address. GitHub limits anonymous requests from one address, and each start makes three or four.
  4. At **Network connection**: the copy of Foundry Connect on the media is missing or failed its integrity check. The log contains "No authenticated original Foundry.Connect payload is available." or "Runtime archive SHA256 does not match the trusted digest."
- **Fix:**
  1. Check that the network allows the hosts in [Network endpoints](../reference/network-endpoints.md). **Network ready** in Foundry Connect does not prove it.
  2. Check the date and time in the firmware of the device.
  3. Restart the device and try again.
  4. If the failure is at **Network connection**, recreate the media.
- **Collect:** `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Foundry Deploy stopped with exit code" and a number

- **Where:** **Deployment application** shows **Failed**.
- **Cause:** Foundry Deploy closed before its window was ready.
- **Fix:**
  1. Restart the device.
  2. If it happens again, recreate the media with the current version of Foundry OSD.
- **Collect:** `FoundryDeploy.log` and `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## "Application readiness was not confirmed within two minutes. The application may still be running."

- **Where:** **Network connection** or **Deployment application** shows **Failed**.
- **Cause:** Foundry Connect or Foundry Deploy did not show a usable window within 2 minutes, for example on a very slow device. The console stops waiting but does not close the application.
- **Fix:**
  1. Look for the application window before doing anything else.
  2. If Foundry Deploy is open, use it normally.
  3. If the stage was **Network connection**, restart the device: the startup no longer waits for Foundry Connect.
- **Collect:** `FoundryBootstrap.log` and the log of the application. See [Logs and support information](logs-and-support.md).

## "Application startup metadata is invalid. Recreate the boot media or refresh the runtime cache."

- **Where:** **Network connection** or **Deployment application** shows **Failed**.
- **Cause:** The package of Foundry Connect or Foundry Deploy that was about to start is damaged.
- **Fix:**
  1. Restart the device with Internet access, so the application is downloaded again.
  2. If it happens again, recreate the media with the current version of Foundry OSD.
- **Collect:** `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## Other warnings

Yellow lines that start with "Warning:" never stop the startup. The console shows three of them at most, then a line such as "2 more warnings are available in the log."

<details>

<summary>All startup warnings</summary>

| Warning | Meaning | What to do |
| --- | --- | --- |
| "Wired AutoConfig service could not be started. Boot will continue." | The Windows PE service for wired 802.1X did not start. A plain wired network still works. | Restart. A wired 802.1X profile cannot authenticate without it. |
| "Wi-Fi AutoConfig service could not be started. Boot will continue." | The Windows PE wireless service did not start. Foundry Connect shows no Wi-Fi list. | Restart. See [The Wi-Fi list is missing](network.md#the-wi-fi-list-is-missing). |
| "System clock could not be corrected. Boot will continue." | The Internet time was read but the clock could not be set. | Set the date and time in the firmware. |
| "Public IP timezone could not be resolved. Boot will continue with UTC." | Automatic time zone detection got no answer. | See [Wrong time or time zone](network.md#the-time-or-the-time-zone-is-wrong-in-windows-pe). |
| "WinPE timezone could not be updated. Boot will continue." | The time zone was found but could not be applied. | Nothing. Times in Windows PE stay in the previous zone. |
| "Embedded deployment timezone configuration could not be read. Boot will continue with automatic detection." | The time zone setting on the media could not be read. | Recreate the media if it repeats. |
| "The verified runtime could not be saved to the cache. This boot can continue." | The downloaded application could not be written to the USB drive. | Nothing for this start. If it repeats, check the USB drive. |
| "Foundry Connect could not be updated. Continuing." | On a USB drive, the check for a newer Foundry Connect failed. | Nothing. |
| "This application does not support startup confirmation. Readiness will remain unverified." | The console cannot confirm that the application opened. The last stage shows **Unverified** and the second line reads "Deployment application launched". | Check that the Foundry Deploy window is open. |
| "Some network preparation was unavailable. Continuing.", "Early clock synchronization was unavailable. Continuing to Foundry Connect." or "Some system preparation was unavailable. Continuing." | A preparation step met an unexpected error. | Nothing, unless a later stage fails. |

</details>
