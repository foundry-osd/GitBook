# Network and Foundry Connect troubleshooting

Use this page when Foundry Connect shows **No internet access** or one of the messages below. [Network readiness](../foundry-connect/network-readiness.md) explains every status of a normal start.

While **No internet access** is shown, the status area offers **Retry**, to check again once you fixed the cause, and **Export diagnostics...**, to save the logs for support. There is no way to continue without Internet access. Foundry Connect writes its log to `X:\Foundry\Logs\FoundryConnect.log`; see [Export logs from Foundry Connect and Foundry Deploy](logs-and-support.md#export-logs-from-foundry-connect-and-foundry-deploy).

| What you see | Go to section |
| --- | --- |
| "New version available. Update Foundry OSD and rebuild boot media." | [New version available](#new-version-available) |
| "No Ethernet adapter detected.", or "No network adapter was detected..." | [No Ethernet adapter](#no-ethernet-adapter) |
| **No active link**, or "No network connection. Connect an Ethernet cable or join a Wi-Fi network." | [No active link](#no-active-link) |
| **Waiting for network configuration**, or "The network did not provide an IP address..." | [Waiting for network configuration](#waiting-for-network-configuration) |
| **802.1X**: **Authentication failed** | [Authentication failed](#wired-authentication-fails) |
| **802.1X**: **Unavailable** | [802.1X unavailable](#wired-dot1x-unavailable) |
| **802.1X**: **Unavailable**, "The deployment password was not entered, so the 802.1X settings were not applied." | [802.1X: deployment password not entered](#wired-dot1x-password-not-entered) |
| "Sign-in page detected", or "This network requires signing in on a web page." | [Sign-in page detected](#captive-portal) |
| "The network is connected but cannot reach the internet..." | [Connected but no Internet access](#internet-not-validated) |
| No **Wi-Fi** card | [The Wi-Fi card is missing](#wi-fi-list-missing) |
| "Wi-Fi support is not available in this boot environment." | [Wi-Fi support is not available](#wi-fi-runtime-missing) |
| "No Wi-Fi adapter is currently detected." | [No Wi-Fi adapter](#no-wi-fi-adapter) |
| "No Wi-Fi networks are currently visible.", or a network missing from the list | [No Wi-Fi networks](#no-wi-fi-networks) |
| "This network requires a provisioned Wi-Fi profile." or "This Wi-Fi network is not supported in this build." | [Network cannot be joined from the list](#requires-provisioned-profile) |
| "Unable to connect. Check the password or network settings and try again." | [Unable to connect](#unable-to-connect) |
| **Provisioned Wi-Fi unavailable** | [Provisioned Wi-Fi unavailable](#provisioned-wifi-unavailable) |
| **Password required** on the **Provisioned Wi-Fi** card | [Provisioned Wi-Fi: deployment password not entered](#provisioned-wifi-password-not-entered) |
| "Unable to apply the provisioned network settings." | [Unable to apply the provisioned settings](#unable-to-apply-provisioned-settings) |
| "The provisioned Wi-Fi profile is not available." | [Provisioned profile not available](#provisioned-profile-not-available) |
| "Unable to connect the provisioned Wi-Fi profile. Try again." | [Unable to connect the provisioned profile](#unable-to-connect-provisioned-profile) |
| "Unable to complete the Wi-Fi action. Try again." or "Unable to disconnect. Try again." | [Unable to complete the Wi-Fi action](#wi-fi-action-fails) |
| The countdown disappears, or does not start | [The countdown disappears](#countdown-disappears) |
| **Network ready**, then downloads fail | [Network ready, then downloads fail](#network-ready-downloads-fail) |
| "Diagnostics export failed" | ["Diagnostics could not be exported."](logs-and-support.md#export-failed) in Logs and support information |
| A wrong time or time zone | [Wrong time or time zone](#wrong-time-or-time-zone) |
| No network in the installed Windows | [The roamed profile does not connect](#roamed-profile-does-not-connect) |

## "New version available. Update Foundry OSD and rebuild boot media." <a href="#new-version-available" id="new-version-available"></a>

- **Where:** Top right of Foundry Connect, next to the version. The text can also read "Update Foundry OSD and rebuild boot media."
- **Cause:** The media was created with a version of Foundry OSD older than the Foundry Connect that is running, or with a version that cannot be identified. This is a notice: it blocks nothing.
- **Fix:** Continue the deployment. Afterwards, the administrator updates Foundry OSD, then recreates the ISO or updates the USB drive.
- **Collect:** Nothing.

## "No Ethernet adapter detected." <a href="#no-ethernet-adapter" id="no-ethernet-adapter"></a>

- **Where:** A line in place of the **Ethernet** card. When the device has no usable Wi-Fi either, the status reads **No internet access** with "No network adapter was detected. Connect a supported Ethernet or Wi-Fi adapter, then select Retry."
- **Cause:**
  1. Windows PE has no driver for the wired adapter, for example a USB-C adapter or a dock.
  2. The adapter is disabled in the firmware of the device.
  3. The device has no wired adapter.
- **Fix:**
  1. Try another adapter, or use Wi-Fi if the **Wi-Fi** card is shown.
  2. The administrator adds the driver with the **WinPE drivers** options or the **Additional WinPE driver folder** on the [General](../foundry-osd/general.md) page, then recreates the media.
- **Collect:** The device model and the adapter model.

## "No active link" <a href="#no-active-link" id="no-active-link"></a>

- **Where:** **Ethernet** card, with "Check the cable connection". The status can read "No network connection. Connect an Ethernet cable or join a Wi-Fi network."
- **Cause:** The adapter is detected but has no link: cable, dock or switch port.
- **Fix:**
  1. Reseat the cable, then try another cable and another port.
  2. Select **Retry**, or wait for the next check, about 10 seconds.
- **Collect:** Nothing.

## "Waiting for network configuration" <a href="#waiting-for-network-configuration" id="waiting-for-network-configuration"></a>

- **Where:** **Ethernet** card, with "Waiting for DHCP or static network configuration" or "Self-assigned address: the DHCP server did not answer". The status can read "The network did not provide an IP address. Check DHCP on this network, then select Retry."
- **Cause:**
  1. The network has no DHCP server for this port or VLAN, or the address pool is empty.
  2. The network requires 802.1X and the device is not authenticated. See [Authentication failed](#wired-authentication-fails).
  3. Network access control keeps unknown devices in a restricted network.
- **Fix:**
  1. Wait about 20 seconds, then select **Retry**.
  2. Try a port that is known to work.
  3. Ask the network team to check DHCP and access control for this port.
- **Collect:** The values of **Adapter**, **IPv4** and **Gateway** in the **Ethernet** card.

## "Authentication failed" <a href="#wired-authentication-fails" id="wired-authentication-fails"></a>

<figure>
  <img src="../.gitbook/assets/foundry-connect-network-troubleshooting-01-dot1x-failed.png" alt="Ethernet card of Foundry Connect with the 802.1X row on Authentication failed and a red box saying that the network rejected the authentication">
  <figcaption>The box under the details gives the reason of the failure.</figcaption>
</figure>

- **Where:** **Ethernet** card, on the **802.1X** row, with a red box that gives the reason. The media was created with [Ethernet 802.1X](../foundry-osd/network/ethernet-802.1x.md). The same causes apply when the row shows **Profile applied** and the card never gets a usable address: the network refused the device without saying so.
- **Cause:**
  1. "The network rejected the authentication. Check the certificate and the switch port settings.": the certificate is unknown to the authentication server or not valid at the date of the device clock, the profile expects a typed user name and password, or the switch port is not set for this device. The client certificate may also not have been imported in Windows PE, for example because of a wrong **PFX password** or a damaged certificate: the log then shows `FailureCode=wired_certificate_import_failed`.
  2. "Windows rejected the 802.1X profile from the boot media.": Windows PE refused the profile file.
  3. "The 802.1X profile is missing from the boot media. Recreate the boot media.": the profile file is not on the media.
- **Fix:**
  1. For the first cause, check the date and time in the firmware, then ask the network team to read the log of the authentication server. Windows PE cannot correct its clock before the device is online. For a certificate import failure, in Foundry OSD, select the certificate again, retype the **PFX password** and recreate the media. The password is empty after a restart of Foundry OSD when **Remember passwords** is off.
  2. For a refused profile, export the profile again from a computer that authenticates on this network, and recreate the media.
  3. For a missing profile, recreate the media.
  4. To continue now, use Wi-Fi or a port without 802.1X.
- **Collect:** `FoundryConnect.log`. See [Failure codes](#failure-codes) and [Logs and support information](logs-and-support.md).

## 802.1X "Unavailable" <a href="#wired-dot1x-unavailable" id="wired-dot1x-unavailable"></a>

- **Where:** **Ethernet** card, on the **802.1X** row, with a yellow box.
- **Cause:** The wired 802.1X settings on the media cannot be used: "The 802.1X certificate password cannot be read. Recreate the boot media." A plain wired network and Wi-Fi keep working.
- **Fix:**
  1. Use Wi-Fi or a port without 802.1X to continue now.
  2. The administrator recreates the media with the current version of Foundry OSD.
- **Collect:** `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## "The deployment password was not entered, so the 802.1X settings were not applied." <a href="#wired-dot1x-password-not-entered" id="wired-dot1x-password-not-entered"></a>

- **Where:** **Ethernet** card, on the **802.1X** row, which shows **Unavailable**. The box goes on: "Ethernet without 802.1X and Wi-Fi still work."
- **Cause:** The wired 802.1X profile uses a client certificate protected by the Deployment password, and **Cancel** was selected in the **Protected network settings** window at startup. Foundry Connect does not apply wired 802.1X for the rest of the session.
- **Fix:**
  1. Restart the device and type the Deployment password when Foundry Connect asks for it. See [Deployment password in Foundry Connect](../foundry-connect/README.md#deployment-password-in-foundry-connect).
  2. To continue now without the password, use Wi-Fi or a port without 802.1X. Foundry Deploy then asks for the password when it opens.
- **Collect:** Nothing. Never write the password in a support request.

## "Sign-in page detected" <a href="#captive-portal" id="captive-portal"></a>

<figure>
  <img src="../.gitbook/assets/foundry-connect-network-troubleshooting-02-captive-portal.png" alt="Foundry Connect on No internet access with This network requires signing in on a web page, the Retry and Export diagnostics buttons, and the Sign-in page detected box">
  <figcaption>A sign-in page counts as no Internet access.</figcaption>
</figure>

- **Where:** A yellow box under the status, which reads **No internet access** with "This network requires signing in on a web page."
- **Cause:** The device is connected, but the network answered the Internet check with its own page instead of the expected answer. This is typical of a guest network that asks you to sign in or accept terms, and of a proxy that asks for credentials. Windows PE has no browser to sign in.
- **Fix:**
  1. Use another network for deployments, such as a wired port or an SSID without a sign-in page.
  2. If this network lets you authorize the device from another computer or phone, do so, then select **Retry**.
  3. Ask the network team to let the deployment network reach the test addresses without a sign-in page. See [What Network ready tests](../foundry-connect/network-readiness.md#what-network-ready-tests).
- **Collect:** `FoundryConnect.log`. Lines that start with "Connectivity endpoint result changed." show, for each test address, whether it answered and with which status.

## "The network is connected but cannot reach the internet." <a href="#internet-not-validated" id="internet-not-validated"></a>

- **Where:** The status reads **No internet access** with "The network is connected but cannot reach the internet. Check the firewall or proxy, then select Retry." The **Ethernet** card shows **Connected**, or Wi-Fi is connected.
- **Cause:** Neither test address answered within 5 seconds.
  1. The network filters outbound HTTP or these two hosts.
  2. Name resolution (DNS) fails.
  3. The network requires an explicit proxy. Foundry has no proxy setting for Windows PE.
- **Fix:**
  1. Ask the network team to allow HTTP requests to `www.msftconnecttest.com` and `www.msftncsi.com` from the deployment network. The test cannot be changed or skipped.
  2. Use another network to continue now, then select **Retry**.
- **Collect:** `FoundryConnect.log`. Lines that start with "Connectivity endpoint result changed." show, for each test address, whether it answered.

## The Wi-Fi card is missing <a href="#wi-fi-list-missing" id="wi-fi-list-missing"></a>

- **Where:** Foundry Connect shows only the **Ethernet** card, and no line about Wi-Fi.
- **Cause:** The media was created without Wi-Fi.
- **Fix:**
  1. Use a wired connection for this deployment.
  2. The administrator turns on Wi-Fi on the [Wi-Fi](../foundry-osd/network/wifi.md) page and recreates the media.
- **Collect:** Nothing.

## "Wi-Fi support is not available in this boot environment." <a href="#wi-fi-runtime-missing" id="wi-fi-runtime-missing"></a>

- **Where:** A line in place of the Wi-Fi cards.
- **Cause:** The wireless service of Windows PE did not start. The startup console may have shown "Warning: Wi-Fi AutoConfig service could not be started. Boot will continue." The boot image can also lack Wi-Fi support, see [What decides the boot image](../foundry-osd/media/create-iso.md#what-decides-the-boot-image).
- **Fix:**
  1. Use a wired connection for this deployment.
  2. Restart the device once.
  3. If the line comes back, the administrator recreates the media.
- **Collect:** `FoundryBootstrap.log` and `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## "No Wi-Fi adapter is currently detected." <a href="#no-wi-fi-adapter" id="no-wi-fi-adapter"></a>

- **Where:** A line in place of the Wi-Fi cards. An attempt to connect can answer "No Wi-Fi adapter is available."
- **Cause:**
  1. Windows PE has no driver for the wireless adapter. Foundry adds a driver package for Intel adapters only.
  2. The wireless adapter is turned off in the firmware or by a hardware switch.
- **Fix:**
  1. Use a wired connection for this deployment.
  2. The administrator puts the adapter driver in the **Additional WinPE driver folder** on the [General](../foundry-osd/general.md) page and recreates the media.
- **Collect:** The device model and the wireless adapter model.

## "No Wi-Fi networks are currently visible." <a href="#no-wi-fi-networks" id="no-wi-fi-networks"></a>

- **Where:** **Wi-Fi** card, or a network you expect is missing from the list.
- **Cause:**
  1. No network is in range of the adapter.
  2. The network hides its name. Hidden networks are not listed.
- **Fix:**
  1. Select the refresh button next to the card title.
  2. Move the device closer to an access point.
  3. For a hidden network, the administrator embeds its profile on the [Wi-Fi](../foundry-osd/network/wifi.md) page and recreates the media.
- **Collect:** Nothing.

## "This network requires a provisioned Wi-Fi profile." <a href="#requires-provisioned-profile" id="requires-provisioned-profile"></a>

- **Where:** Under the network selected in the **Wi-Fi** list. **Connect** stays unavailable. The text can also read "This Wi-Fi network is not supported in this build."
- **Cause:** Only open, Enhanced Open, WPA2 Personal and WPA3 Personal networks can be joined from the list. The first text means an enterprise (802.1X) network; the second a WEP or WPA Personal network.
- **Fix:**
  1. Use a wired connection or another network.
  2. For an enterprise network, the administrator embeds a profile on the [Wi-Fi](../foundry-osd/network/wifi.md) page and recreates the media.
- **Collect:** Nothing.

## "Unable to connect. Check the password or network settings and try again." <a href="#unable-to-connect" id="unable-to-connect"></a>

- **Where:** Under the network selected in the **Wi-Fi** list, after **Connect**. Three related texts appear in the same place: "The connection attempt timed out. Move closer to the access point and try again.", "The password does not meet the requirements of this network." and "This network is no longer visible. Refresh the list and try again."
- **Cause:**
  1. The password is wrong, or Windows PE refused the network settings.
  2. The connection was not established within about 20 seconds, for example with a weak signal.
  3. The password does not have 8 to 63 characters.
  4. The network went out of range.
- **Fix:**
  1. Type the password again. The eye button shows it.
  2. Move closer to the access point and select **Connect** again.
  3. Select the refresh button next to the card title, then select the network again.
- **Collect:** `FoundryConnect.log`. The `FailureCode=` value names the step that failed: see [Failure codes](#failure-codes).

## "Provisioned Wi-Fi unavailable" <a href="#provisioned-wifi-unavailable" id="provisioned-wifi-unavailable"></a>

- **Where:** A yellow box in the **Provisioned Wi-Fi** card, with the reason.
- **Cause:** The Wi-Fi profile on the media cannot be used:
  1. "The provisioned Wi-Fi password cannot be read from this boot media. Recreate the boot media. Ethernet and other Wi-Fi networks still work."
  2. "The enterprise Wi-Fi profile is missing from the boot media. Recreate the boot media."
  3. "The provisioned Wi-Fi settings are not valid. Recreate the boot media."
  4. "The provisioned Wi-Fi password does not meet the requirements of the network. Recreate the boot media."
- **Fix:**
  1. Use a wired connection, or join a network from the **Wi-Fi** list, to continue now.
  2. The administrator checks the profile on the [Wi-Fi](../foundry-osd/network/wifi.md) page and recreates the media.
- **Collect:** `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## "The deployment password was not entered. Select Connect to enter it." <a href="#provisioned-wifi-password-not-entered" id="provisioned-wifi-password-not-entered"></a>

- **Where:** A box in the **Provisioned Wi-Fi** card, which shows **Password required**. The box goes on: "Ethernet and other Wi-Fi networks still work." Before any password was asked, the box reads "The provisioned Wi-Fi is protected by the deployment password. Select Connect to enter it."
- **Cause:** The provisioned Wi-Fi uses a passphrase or a client certificate protected by the Deployment password, and **Cancel** was selected in the **Protected network settings** window. Foundry Connect does not ask again by itself.
- **Fix:**
  1. Select **Connect** on the **Provisioned Wi-Fi** card, type the Deployment password and select **Continue**. The profile then connects.
  2. If the window reads "The password is incorrect. Try again.", check the keyboard layout in the **Keyboard** menu and type the password again.
  3. To continue without the password, use a wired connection or join a network from the **Wi-Fi** list. Foundry Deploy then asks for the password when it opens.
- **Collect:** Nothing. Never write the password in a support request.

## "Unable to apply the provisioned network settings." <a href="#unable-to-apply-provisioned-settings" id="unable-to-apply-provisioned-settings"></a>

- **Where:** **Provisioned Wi-Fi** card, when Foundry Connect opens.
- **Cause:** The Wi-Fi certificate or the Wi-Fi profile from the media could not be installed, for example a missing or wrong **PFX password**.
- **Fix:**
  1. Join a network from the **Wi-Fi** list, or use a wired connection, to continue now.
  2. Export the logs and find the `FailureCode=` value in `FoundryConnect.log`: see [Failure codes](#failure-codes).
  3. The administrator selects the file again, retypes the **PFX password** and recreates the media.
- **Collect:** `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## "The provisioned Wi-Fi profile is not available." <a href="#provisioned-profile-not-available" id="provisioned-profile-not-available"></a>

- **Where:** **Provisioned Wi-Fi** card, after **Connect**.
- **Cause:** The profile announced by the media cannot be used, for example because its file is missing.
- **Fix:**
  1. Join a network from the **Wi-Fi** list, or use a wired connection.
  2. The administrator recreates the media.
- **Collect:** `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## "Unable to connect the provisioned Wi-Fi profile. Try again." <a href="#unable-to-connect-provisioned-profile" id="unable-to-connect-provisioned-profile"></a>

- **Where:** **Provisioned Wi-Fi** card. While the device has no Internet access, Foundry Connect tries again by itself, about every 20 seconds at first and then less often, until you connect or disconnect a network yourself. The card can also show **Not in range**.
- **Cause:**
  1. The network is out of range.
  2. The **SSID** typed in Foundry OSD differs from the broadcast name. Upper and lower case count.
  3. The passphrase on the media is wrong.
  4. An enterprise network refused the device: certificate, device clock, or a profile that expects typed credentials.
- **Fix:**
  1. Check that the network name appears in the **Wi-Fi** list.
  2. To try again at once, select **Connect** on the **Provisioned Wi-Fi** card.
  3. To continue now, join a passphrase network from the list or use a wired connection.
  4. The administrator corrects the profile on the [Wi-Fi](../foundry-osd/network/wifi.md) page and recreates the media.
- **Collect:** `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## "Unable to complete the Wi-Fi action. Try again." <a href="#wi-fi-action-fails" id="wi-fi-action-fails"></a>

- **Where:** Under the selected network or on the **Provisioned Wi-Fi** card. The text can also read "Unable to disconnect. Try again." or "Unable to disconnect the provisioned Wi-Fi profile. Try again."
- **Cause:** The request met an unexpected error, or the adapter did not disconnect within about 10 seconds.
- **Fix:**
  1. Select **Tools > Refresh status**, then repeat the action.
  2. If it repeats, restart the device.
- **Collect:** `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## The "Continuing automatically" countdown disappears <a href="#countdown-disappears" id="countdown-disappears"></a>

- **Where:** Under **Continue**, in the status area.
- **Cause:**
  1. A check made during the countdown found no Internet access: the connection is unstable. The countdown starts again at the next **Network ready**.
  2. You worked on the network, the Deployment password was asked, or you selected **Stay here**. The countdown stays off for the session, by design: "Automatic continue is paused because you changed network settings." or "Automatic continue is off. Select Continue when you are ready."
- **Fix:**
  1. Select **Continue** when you are ready.
  2. If the connection keeps dropping, check the cable or move closer to the access point.
- **Collect:** Nothing.

## Network ready appears, then downloads fail <a href="#network-ready-downloads-fail" id="network-ready-downloads-fail"></a>

- **Where:** After **Continue**: the startup console fails at **Deployment files**, or Foundry Deploy cannot load its catalogs or download Windows.
- **Cause:** **Network ready** only proves that one Microsoft test address answered. A firewall or a filtering proxy can let that test through while GitHub and the Microsoft download hosts stay blocked.
- **Fix:**
  1. Check that the network allows the hosts in [Network endpoints](../reference/network-endpoints.md).
  2. See ["This boot stage could not be completed. Check the session log for details."](windows-pe-startup.md#boot-stage-not-completed) for the startup console, and [Next stays unavailable on Target device](deployment/before-deployment-starts.md#next-unavailable) or [Download fails](deployment/checks-and-image-download.md#image-source-unavailable) for Foundry Deploy.
- **Collect:** `FoundryBootstrap.log` and `FoundryDeploy.log`. See [Logs and support information](logs-and-support.md).

## The time or the time zone is wrong in Windows PE <a href="#wrong-time-or-time-zone" id="wrong-time-or-time-zone"></a>

- **Where:** Times shown in Foundry Connect and Foundry Deploy, or a certificate refused because of its dates.
- **Cause:** The clock and the automatic time zone come from the Internet: see [When the clock and the time zone are set](../foundry-connect/windows-pe-startup.md#when-the-clock-and-the-time-zone-are-set). On a filtered network the firmware clock and UTC are kept. The automatic time zone is also wrong when the Internet exit of the site is in another region.
- **Fix:**
  1. Set the date and time in the firmware of the device.
  2. The administrator chooses a fixed [Windows PE time zone](../foundry-osd/general.md#windows-pe-time-zone) and recreates the media.
- **Collect:** `FoundryBootstrap.log`. See [Logs and support information](logs-and-support.md).

## The roamed profile does not connect after deployment <a href="#roamed-profile-does-not-connect" id="roamed-profile-does-not-connect"></a>

- **Where:** The installed Windows, at the out-of-box experience (OOBE): no network, although [Windows profile roaming](../foundry-osd/network/README.md#windows-profile-roaming) was turned on.
- **Cause:**
  1. The Wi-Fi network was never joined successfully in Foundry Connect, so it was not roamed.
  2. An enterprise Wi-Fi profile is imported but not connected by Foundry.
  3. A wired 802.1X profile connects only when Windows holds the computer credential it requires.
  4. A `.pfx` client certificate is copied only with **Include private-key certificate material**.
  5. The import failed. This is reported as a warning and does not stop the deployment.
- **Fix:**
  1. Check the roaming settings on the Network pages of Foundry OSD against the table in [Windows profile roaming](../foundry-osd/network/README.md#windows-profile-roaming).
  2. Read `Foundry.PostInstall.log` on the device. See [After the restart troubleshooting](after-the-restart.md).
- **Collect:** `Foundry.PostInstall.log`. See [Log locations](logs-and-support.md#log-locations).

## Failure codes in the log <a href="#failure-codes" id="failure-codes"></a>

When a network step fails, `FoundryConnect.log` contains a line that starts with "Network operation failed." and holds `FailureCode=` followed by one of these values. Foundry Connect does not write Wi-Fi network names in its log.

<details>

<summary>Failure codes</summary>

| Code | Step that failed |
| --- | --- |
| `wired_profile_template_missing` | The wired 802.1X profile file is not on the media. |
| `wired_secret_unavailable` | The client certificate or its password for wired 802.1X cannot be read from the media. |
| `wired_certificate_import_failed` | The certificate for wired 802.1X could not be imported. |
| `wired_profile_import_failed` | Windows PE rejected the wired 802.1X profile file. |
| `wired_reconnect_request_failed` | The wired adapter could not be asked to reconnect. |
| `wifi_secret_unavailable` | The Wi-Fi password, or the client certificate or its password, cannot be read from the media. |
| `wifi_secret_locked` | The provisioned Wi-Fi could not connect because the Deployment password was not entered. |
| `wifi_certificate_import_failed` | The certificate for Wi-Fi could not be imported. |
| `wifi_profile_import_failed` | Windows PE rejected the Wi-Fi profile. |
| `wifi_profile_unavailable` | The media carries no usable Wi-Fi profile. |
| `wifi_runtime_missing` | The wireless service of Windows PE is not available. |
| `wifi_runtime_not_supported` | The selected network uses a security that cannot be joined from the list. |
| `wifi_passphrase_invalid` | The password does not meet the rules of the network. |
| `wifi_network_not_visible` | The network is no longer in range. |
| `wifi_connect_request_failed` | Windows PE refused the connection request. |
| `wifi_connect_failed` | The network refused the connection, for example a wrong password. |
| `wifi_connect_timeout` | The network was not connected within about 20 seconds. |
| `no_wireless_adapter` | No wireless adapter is available. |
| `wifi_disconnect_request_failed` | Windows PE refused the disconnect request. |
| `wifi_disconnect_timeout` | The network was still connected after about 10 seconds. |
| `network_unexpected` | An unexpected error. Export the logs for support. |

</details>
