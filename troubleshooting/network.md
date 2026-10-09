# Network and Foundry Connect troubleshooting

Use this page when Foundry Connect does not reach **Network ready** or shows one of the messages below. [Network readiness](../foundry-connect/network-readiness.md) explains every status of a normal start.

Foundry Connect writes its log to `X:\Foundry\Logs\FoundryConnect.log`. To take it off the device, select **Tools > Export diagnostics...**. See [Export logs from Foundry Connect and Foundry Deploy](logs-and-support.md#export-logs-from-foundry-connect-and-foundry-deploy).

| What you see | Go to section |
| --- | --- |
| "New version available. Update Foundry OSD and rebuild boot media." | [New version available](#new-version-available) |
| "No Ethernet adapter detected." | [No Ethernet adapter](#no-ethernet-adapter) |
| "No active link", "Check the cable connection" | [No active link](#no-active-link) |
| "Waiting for network configuration", or an **IPv4** value that starts with 169.254 | [Waiting for network configuration](#waiting-for-network-configuration) |
| A wired 802.1X network, no usable address and no message | [Wired 802.1X does not authenticate](#wired-authentication-fails) |
| No **Wi-Fi** list | [The Wi-Fi list is missing](#wi-fi-list-missing) |
| "No Wi-Fi adapter is currently detected." | [No Wi-Fi adapter](#no-wi-fi-adapter) |
| "No Wi-Fi networks are currently visible." | [No Wi-Fi networks](#no-wi-fi-networks) |
| "This network requires a provisioned Wi-Fi profile." | [Network requires a provisioned profile](#requires-provisioned-profile) |
| "Unable to connect. Check the password or network settings and try again." | [Unable to connect](#unable-to-connect) |
| "Unable to apply the provisioned network settings." | [Unable to apply the provisioned settings](#unable-to-apply-provisioned-settings) |
| "The provisioned Wi-Fi profile is not available." | [Provisioned profile not available](#provisioned-profile-not-available) |
| "Unable to connect the provisioned Wi-Fi profile. Try again." | [Unable to connect the provisioned profile](#unable-to-connect-provisioned-profile) |
| "Unable to complete the Wi-Fi action. Try again." or "Unable to disconnect. Try again." | [Unable to complete the Wi-Fi action](#wi-fi-action-fails) |
| **Waiting for network** although the device is connected | [Internet access has not been validated](#internet-not-validated) |
| The countdown disappears | [The countdown disappears](#countdown-disappears) |
| "Network refresh failed" | [Network refresh failed](#network-refresh-failed) |
| **Network ready**, then downloads fail | [Network ready, then downloads fail](#network-ready-downloads-fail) |
| "Diagnostics export failed" | [Diagnostics export failed](#diagnostics-export-failed) |
| A wrong time or time zone | [Wrong time or time zone](#wrong-time-or-time-zone) |
| No network in the installed Windows | [The roamed profile does not connect](#roamed-profile-does-not-connect) |

## "New version available. Update Foundry OSD and rebuild boot media." <a href="#new-version-available" id="new-version-available"></a>

- **Where:** Top right of Foundry Connect, next to the version. The text can also read "Update Foundry OSD and rebuild boot media."
- **Cause:** The media was created with a version of Foundry OSD older than the Foundry Connect that is running, or with a version that cannot be identified. This is a notice: it blocks nothing.
- **Fix:** Continue the deployment. Afterwards, the administrator updates Foundry OSD, then recreates the ISO or updates the USB drive.
- **Collect:** Nothing.

## "No Ethernet adapter detected." <a href="#no-ethernet-adapter" id="no-ethernet-adapter"></a>

- **Where:** **Ethernet** card.
- **Cause:**
  1. Windows PE has no driver for the wired adapter, for example a USB-C adapter or a dock.
  2. The adapter is disabled in the firmware of the device.
  3. The device has no wired adapter.
- **Fix:**
  1. Try another adapter, or use Wi-Fi if the **Wi-Fi** list is shown.
  2. The administrator adds the driver with the driver options or the **Custom driver folder** on the [General](../foundry-osd/general.md) page, then recreates the media.
- **Collect:** The device model and the adapter model.

## "No active link" <a href="#no-active-link" id="no-active-link"></a>

- **Where:** **Ethernet** card, with "Check the cable connection".
- **Cause:** The adapter is detected but has no link: cable, dock or switch port.
- **Fix:**
  1. Reseat the cable, then try another cable and another port.
  2. Wait 10 seconds or select **Tools > Refresh status**.
- **Collect:** Nothing.

## "Waiting for network configuration" <a href="#waiting-for-network-configuration" id="waiting-for-network-configuration"></a>

- **Where:** **Ethernet** card, with "Waiting for DHCP or static network configuration". The same causes apply when the card shows **Connected** with an **IPv4** value that starts with `169.254`: Windows assigned that address itself because no DHCP server answered.
- **Cause:**
  1. The network has no DHCP server for this port or VLAN, or the address pool is empty.
  2. The network requires 802.1X and the device is not authenticated. See the next section.
  3. Network access control keeps unknown devices in a restricted network.
- **Fix:**
  1. Wait for two checks, about 20 seconds.
  2. Try a port that is known to work.
  3. Ask the network team to check DHCP and access control for this port.
- **Collect:** The values of **Adapter**, **IPv4** and **Gateway**.

## Wired 802.1X does not authenticate and no error is shown <a href="#wired-authentication-fails" id="wired-authentication-fails"></a>

- **Where:** The media was created with [Ethernet 802.1X](../foundry-osd/network/ethernet-802.1x.md). The **Ethernet** card does not get a usable address: it may stay on "Waiting for network configuration", or show **Connected** with an **IPv4** value that starts with `169.254`. Which of the two appears is not confirmed on a device. Foundry Connect shows a message for this step only when the media also carries a Wi-Fi profile: see ["Unable to apply the provisioned network settings."](#unable-to-apply-provisioned-settings)
- **Cause:**
  1. The certificate or the profile could not be imported in Windows PE: missing or wrong **PFX password**, damaged certificate, or a profile file that Windows rejects.
  2. The network refused the device: certificate unknown to the authentication server, certificate not valid at the date of the device clock, or a profile that expects a typed user name and password.
- **Fix:**
  1. Export the logs and open `FoundryConnect.log` on another computer. Search for `FailureCode=wired_`.
  2. `wired_certificate_import_failed`: in Foundry OSD, select the certificate again, retype the **PFX password**, and recreate the media. The password is not kept when Foundry OSD closes.
  3. `wired_profile_import_failed`: export the profile again from a computer that authenticates on this network, and recreate the media.
  4. `wired_profile_template_missing`: recreate the media.
  5. No `wired_` line: the imports worked and the network refused the device. Check the date and time in the firmware, then the log of the authentication server. Windows PE cannot correct its clock before the device is online.
- **Collect:** `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## The Wi-Fi list is missing <a href="#wi-fi-list-missing" id="wi-fi-list-missing"></a>

- **Where:** Foundry Connect shows only the **Ethernet** card.
- **Cause:**
  1. The media was created without Wi-Fi.
  2. The wireless service of Windows PE did not start. The startup console then showed "Warning: Wi-Fi AutoConfig service could not be started. Boot will continue."
- **Fix:**
  1. Use a wired connection for this deployment.
  2. The administrator turns on Wi-Fi on the [Wi-Fi](../foundry-osd/network/wifi.md) page and recreates the media.
  3. If Wi-Fi was already enabled, restart the device once.
- **Collect:** `FoundryBootstrap.log` and `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## "No Wi-Fi adapter is currently detected." <a href="#no-wi-fi-adapter" id="no-wi-fi-adapter"></a>

- **Where:** **Wi-Fi** list. The **Provisioned Wi-Fi** card shows "No Wi-Fi adapter available", and an attempt to connect answers "No Wi-Fi adapter is available."
- **Cause:**
  1. Windows PE has no driver for the wireless adapter. Foundry adds a driver package for Intel adapters only.
  2. The wireless adapter is turned off in the firmware or by a hardware switch.
- **Fix:**
  1. Use a wired connection for this deployment.
  2. The administrator puts the adapter driver in the **Custom driver folder** on the [General](../foundry-osd/general.md) page and recreates the media.
- **Collect:** The device model and the wireless adapter model.

## "No Wi-Fi networks are currently visible." <a href="#no-wi-fi-networks" id="no-wi-fi-networks"></a>

- **Where:** **Wi-Fi** list.
- **Cause:** No network is in range of the adapter.
- **Fix:**
  1. Select the refresh button above the list.
  2. Move the device closer to an access point.
- **Collect:** Nothing.

## "This network requires a provisioned Wi-Fi profile." <a href="#requires-provisioned-profile" id="requires-provisioned-profile"></a>

- **Where:** Under the network selected in the **Wi-Fi** list. There is no **Connect** button.
- **Cause:** Only open, OWE and passphrase networks can be joined from the list. The selected network uses another type of security, such as enterprise (802.1X) or WEP.
- **Fix:**
  1. Use a wired connection or another network.
  2. For an enterprise network, the administrator embeds a profile on the [Wi-Fi](../foundry-osd/network/wifi.md) page and recreates the media.
- **Collect:** Nothing.

## "Unable to connect. Check the password or network settings and try again." <a href="#unable-to-connect" id="unable-to-connect"></a>

- **Where:** Under the network selected in the **Wi-Fi** list, after **Connect**.
- **Cause:**
  1. The passphrase is wrong.
  2. The connection was not established within 15 seconds, for example with a weak signal.
  3. Windows PE refused the network settings.
- **Fix:**
  1. Type the passphrase again. The eye button shows it.
  2. Move closer to the access point and select **Connect** again.
- **Collect:** `FoundryConnect.log`. The `FailureCode=` value names the step that failed: see [Failure codes](#failure-codes).

## "Unable to apply the provisioned network settings." <a href="#unable-to-apply-provisioned-settings" id="unable-to-apply-provisioned-settings"></a>

- **Where:** **Provisioned Wi-Fi** card, when Foundry Connect opens.
- **Cause:** A certificate or a profile from the media could not be imported: the Wi-Fi certificate (missing or wrong **PFX password**), the Wi-Fi profile, or the wired 802.1X profile when the media carries both.
- **Fix:**
  1. Join a network from the **Wi-Fi** list, or use a wired connection, to continue now.
  2. Export the logs and find the `FailureCode=` value in `FoundryConnect.log`: see [Failure codes](#failure-codes).
  3. The administrator selects the file again, retypes the **PFX password** and recreates the media.
- **Collect:** `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## "The provisioned Wi-Fi profile is not available." <a href="#provisioned-profile-not-available" id="provisioned-profile-not-available"></a>

- **Where:** **Provisioned Wi-Fi** card.
- **Cause:** The profile announced by the media is incomplete: its file is missing, or it has no SSID or security type.
- **Fix:**
  1. Join a network from the **Wi-Fi** list, or use a wired connection.
  2. The administrator recreates the media.
- **Collect:** `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## "Unable to connect the provisioned Wi-Fi profile. Try again." <a href="#unable-to-connect-provisioned-profile" id="unable-to-connect-provisioned-profile"></a>

- **Where:** **Provisioned Wi-Fi** card. The message comes back about every 20 seconds, because Foundry Connect retries by itself.
- **Cause:**
  1. The network is out of range.
  2. The **SSID** typed in Foundry OSD differs from the broadcast name. Upper and lower case count.
  3. The passphrase on the media is wrong.
  4. An enterprise network refused the device: certificate, device clock, or a profile that expects typed credentials.
- **Fix:**
  1. Check that the network name appears in the **Wi-Fi** list.
  2. To continue now, join a passphrase network from the list or use a wired connection.
  3. The administrator corrects the profile on the [Wi-Fi](../foundry-osd/network/wifi.md) page and recreates the media.
- **Collect:** `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## "Unable to complete the Wi-Fi action. Try again." <a href="#wi-fi-action-fails" id="wi-fi-action-fails"></a>

- **Where:** Under the selected network or on the **Provisioned Wi-Fi** card. The text can also read "Unable to disconnect. Try again." or "Unable to disconnect the provisioned Wi-Fi profile. Try again."
- **Cause:** The request met an unexpected error, or the adapter did not disconnect within 15 seconds.
- **Fix:**
  1. Select **Tools > Refresh status**, then repeat the action.
  2. If it repeats, restart the device.
- **Collect:** `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## "Internet access has not been validated yet." <a href="#internet-not-validated" id="internet-not-validated"></a>

- **Where:** The header stays on **Waiting for network** although the **Ethernet** card shows **Connected** with an IPv4 address, or Wi-Fi is connected.
- **Cause:** Neither test address answered within 5 seconds. Foundry Connect requests `http://www.msftconnecttest.com/connecttest.txt`, then `http://www.google.com`.
  1. The network filters outbound HTTP or these two hosts.
  2. Name resolution (DNS) fails.
  3. The network requires an explicit proxy. Foundry has no proxy setting for Windows PE.
  4. A captive portal answers with an error instead of a page.
- **Fix:**
  1. Ask the network team to allow HTTP requests to at least one of the two addresses from the deployment network. The test cannot be changed or skipped.
  2. Use another network to continue now.
- **Collect:** `FoundryConnect.log`. Lines that start with "Internet probe failed." or "Internet probe timed out." name the address and the type of failure.

## The "Continuing automatically" countdown disappears <a href="#countdown-disappears" id="countdown-disappears"></a>

- **Where:** Header, next to **Continue**.
- **Cause:** A check made during the 10 seconds of the countdown found no Internet access. The connection is unstable.
- **Fix:**
  1. Wait for **Network ready** to come back: the countdown restarts.
  2. If it keeps happening, check the cable or move closer to the access point.
- **Collect:** Nothing.

## "Network refresh failed" <a href="#network-refresh-failed" id="network-refresh-failed"></a>

- **Where:** Header, with a technical error text under it.
- **Cause:** Foundry Connect could not read the state of the network adapters.
- **Fix:**
  1. Select **Tools > Refresh status**.
  2. If the message stays, restart the device.
- **Collect:** The error text and `FoundryConnect.log`. See [Logs and support information](logs-and-support.md).

## Network ready appears, then downloads fail <a href="#network-ready-downloads-fail" id="network-ready-downloads-fail"></a>

- **Where:** After **Continue**: the startup console fails at **Deployment files**, or Foundry Deploy cannot load its catalogs or download Windows.
- **Cause:** **Network ready** only proves that one test address answered. A captive portal or a filtering proxy can answer that test with its own page while GitHub and Microsoft stay blocked.
- **Fix:**
  1. Use a network without a sign-in page for deployments.
  2. Check that the network allows the hosts in [Network endpoints](../reference/network-endpoints.md).
  3. See ["This boot stage could not be completed. Check the session log for details."](windows-pe-startup.md#boot-stage-not-completed) and [Windows deployment troubleshooting](deployment.md).
- **Collect:** `FoundryBootstrap.log` and `FoundryDeploy.log`. See [Logs and support information](logs-and-support.md).

## "Diagnostics export failed" <a href="#diagnostics-export-failed" id="diagnostics-export-failed"></a>

- **Where:** A dialog after **Tools > Export diagnostics...**, with "Diagnostics could not be exported. Check the log for details."
- **Cause:** In Windows PE, the export is written to the **Foundry Cache** partition of the USB drive or, without one, to the first removable drive. Neither exists, for example after a start from an ISO or from the network.
- **Fix:** Plug in a USB drive, then export again.
- **Collect:** Nothing.

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
  1. An enterprise Wi-Fi profile is imported but not connected by Foundry.
  2. A wired 802.1X profile connects only when Windows holds the computer credential it requires.
  3. A `.pfx` client certificate is copied only with **Include private-key certificate material**.
  4. The import failed. This is reported as a warning and does not stop the deployment.
- **Fix:**
  1. Check the roaming settings on the Network pages of Foundry OSD against the table in [Windows profile roaming](../foundry-osd/network/README.md#windows-profile-roaming).
  2. Read `Foundry.PostInstall.log` on the device. See [After the restart troubleshooting](after-the-restart.md).
- **Collect:** `Foundry.PostInstall.log`. See [Log locations](logs-and-support.md#log-locations).

## Failure codes in the log <a href="#failure-codes" id="failure-codes"></a>

When a network step fails, `FoundryConnect.log` contains a line that starts with "Network operation failed." and ends with `FailureCode=` and one of these values.

<details>

<summary>Failure codes</summary>

| Code | Step that failed |
| --- | --- |
| `wired_profile_template_missing` | The wired 802.1X profile file is not on the media. |
| `wired_certificate_import_failed` | The certificate for wired 802.1X could not be imported. |
| `wired_profile_import_failed` | Windows PE rejected the wired 802.1X profile file. |
| `wired_reconnect_request_failed` | The wired adapter could not be asked to reconnect. |
| `wifi_certificate_import_failed` | The certificate for Wi-Fi could not be imported. |
| `wifi_profile_import_failed` | Windows PE rejected the Wi-Fi profile, after 3 attempts. |
| `wifi_profile_unavailable` | The media carries no usable Wi-Fi profile. |
| `wifi_connect_request_failed` | Windows PE refused the connection request. |
| `wifi_connect_timeout` | The network was not connected within 15 seconds. |
| `no_wireless_adapter` | No wireless adapter is available. |

</details>
