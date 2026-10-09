# Network readiness

Foundry Connect shows the state of the wired and wireless connections of the target device, and lets you continue once it has reached the Internet.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-connect-network-readiness-01-ready.png`
- **Capture:** Show Foundry Connect in Windows PE, on media with Wi-Fi enabled: **Network ready**, **Continue** and the countdown, the **Ethernet** card on **Connected**, the **Provisioned Wi-Fi** card and the **Wi-Fi** list. No **Debug** menu. Redact SSIDs and IP addresses.
{% endhint %}

## Join a Wi-Fi network

With a cable and DHCP, or a profile from the media, you have nothing to do. To join a Wi-Fi network yourself:

1. Select the network in the **Wi-Fi** list. Use the refresh button above the list if it is missing.
2. Type its passphrase. The eye button shows what you typed.
3. Select **Connect**.

The **Wi-Fi** list appears only when the media was created with Wi-Fi enabled. You can join open, OWE and passphrase networks. An enterprise network needs a profile from the media.

The network you join can be kept in the installed Windows ([Windows profile roaming](../foundry-osd/network/README.md#windows-profile-roaming)): type only a passphrase that may stay on the device.

## What Network ready tests

Every 10 seconds, and when you select **Tools > Refresh status**, Foundry Connect requests `http://www.msftconnecttest.com/connecttest.txt`. If no successful answer (an HTTP 2xx status) comes within 5 seconds, it requests `http://www.google.com` the same way. Redirects are followed. One success is enough for **Network ready**.

Nothing else is tested, and the test cannot be changed or skipped. **Network ready** does not prove that the other hosts in [Network endpoints](../reference/network-endpoints.md) are reachable.

## What the statuses mean

| On screen | Meaning | What to do |
| --- | --- | --- |
| **Waiting for network**, "Internet access has not been validated yet." | Neither test address has answered yet. | Check the cards below. |
| **Network ready**, "Internet access has been validated. You can continue now." | A test address answered. | Select **Continue** or wait. |
| **Ethernet**, **Wi-Fi ·** SSID or **Provisioned Wi-Fi ·** SSID under the status | The connection in use. | Nothing. |
| **No Ethernet adapter detected.** | Windows PE sees no wired adapter: missing driver, or adapter disabled in the firmware. | Use Wi-Fi or another adapter. |
| **No active link**, **Check the cable connection** | The adapter has no link. | Check the cable, the dock and the switch port. |
| **Waiting for network configuration**, **Waiting for DHCP or static network configuration** | The link is up but the adapter has no IPv4 address. A failed 802.1X authentication may look the same. | Check DHCP on this network. |
| **Connected**, **DHCP lease detected** or **Static network configuration detected** | The adapter has an IPv4 address. | If the header still waits: an **IPv4** value that starts with `169.254` means that no DHCP server answered. With any other address, the test itself gets no answer: see [Internet access has not been validated](../troubleshooting/network.md#internet-not-validated). |

**Adapter**, **IPv4** and **Gateway** describe the wired adapter only. They show **Unavailable** when the device is connected over Wi-Fi.

<details>

<summary>Wi-Fi and Provisioned Wi-Fi statuses</summary>

| On screen | Meaning |
| --- | --- |
| **No Wi-Fi adapter is currently detected.** | Windows PE sees no wireless adapter. |
| **No Wi-Fi networks are currently visible.** | No network in range. Select the refresh button above the list. |
| **Hidden network** | A network that does not broadcast its name. |
| **This network requires a provisioned Wi-Fi profile.** | The selected network, for example an enterprise one, cannot be joined from the list. |
| **No provisioned Wi-Fi profile is available in this boot image.** | The media has Wi-Fi support but no embedded profile. Join a network from the **Wi-Fi** list. |
| **Ready to connect** | The embedded profile is not connected. Foundry Connect tries it by itself while the device has no Internet access. |
| **Connected** | The device is connected with the embedded profile. |
| **Another Wi-Fi network is active** | The device uses a network you joined from the list, not the embedded profile. This is not an error. |
| **No Wi-Fi adapter available** | The embedded profile cannot be used: Windows PE sees no wireless adapter. |
| **Boot image settings**, **Enterprise profile** | The origin of the embedded profile: typed fields, or an exported profile file. |

</details>

## If you cannot continue

Find the text you see on screen in [Network and Foundry Connect troubleshooting](../troubleshooting/network.md).

## Next step

The [startup console](windows-pe-startup.md) prepares Foundry Deploy, then [Foundry Deploy](../foundry-deploy/README.md) opens.
