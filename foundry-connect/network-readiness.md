# Network readiness

Foundry Connect shows the state of the wired and wireless connections of the target device, and lets you continue once it has reached the Internet.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-connect-network-readiness-01-ready.png`
- **Capture:** Show Foundry Connect in Windows PE, started from deployment media with Wi-Fi enabled: **Network ready** with the **Continue** button and the **Continuing automatically in** countdown, the **Ethernet** card showing **Connected**, the **Provisioned Wi-Fi** card and the **Wi-Fi** list. No **Debug** menu. Redact SSIDs and IP addresses.
{% endhint %}

## What to do

1. Wait. On a wired network with DHCP, or with a profile from the media, the header changes to **Network ready** without any action.
2. If the header stays on **Waiting for network**, read the **Ethernet** card and compare it with the table below.
3. To use Wi-Fi, select the network in the **Wi-Fi** list, type its passphrase and select **Connect**. The eye button shows what you typed. Use the refresh button above the list if the network is missing.
4. When **Network ready** appears, select **Continue**, or let the 10 second countdown finish.

The **Wi-Fi** list and the **Provisioned Wi-Fi** card appear only when the media was created with Wi-Fi enabled. From the list you can join open, OWE and passphrase networks. An enterprise network needs a profile from the media.

A network you join by typing a passphrase can be kept in the installed Windows: see [Windows profile roaming](../foundry-osd/network/README.md#windows-profile-roaming). Use only a passphrase that may stay on the device.

## What Network ready tests

Every 10 seconds, and when you select **Tools > Refresh status**, Foundry Connect requests two web addresses over HTTP (port 80), in this order:

1. `http://www.msftconnecttest.com/connecttest.txt`
2. `http://www.google.com`

Each request waits up to 5 seconds and follows redirects. One successful answer (an HTTP 2xx status) from either address is enough for **Network ready**. Nothing else is tested, and the test cannot be changed or skipped.

So **Network ready** does not prove that the other hosts in [Network endpoints](../reference/network-endpoints.md) are reachable. Foundry has no proxy setting for Windows PE, and the proxy configured in Foundry OSD is not copied to the media: plan for direct or transparent access.

## What the statuses mean

| On screen | Meaning | What to do |
| --- | --- | --- |
| **Waiting for network**, "Internet access has not been validated yet." | Neither test address has answered yet. | Check the cards below. |
| **Network ready**, "Internet access has been validated. You can continue now." | A test address answered. | Select **Continue** or wait. |
| **Ethernet**, **Wi-Fi ·** SSID or **Provisioned Wi-Fi ·** SSID under the status | The connection in use. | Nothing. |
| **No Ethernet adapter detected.** | Windows PE sees no wired adapter: its driver is missing from the media, or the adapter is disabled in the firmware. | Use Wi-Fi or another adapter, and report the model to the administrator. |
| **No active link**, **Check the cable connection** | The adapter has no link. | Check the cable, the dock and the switch port. |
| **Waiting for network configuration**, **Waiting for DHCP or static network configuration** | The link is up but the adapter has no IPv4 address. A failed 802.1X authentication looks the same. | Check DHCP on this network. |
| **Connected**, **DHCP lease detected** or **Static network configuration detected** | The adapter has an IPv4 address. | If the header still waits, the network filters the test addresses. |

**Adapter**, **IPv4** and **Gateway** describe the wired adapter only. They show **Unavailable** when the device is connected over Wi-Fi.

<details>

<summary>Wi-Fi and Provisioned Wi-Fi statuses</summary>

| On screen | Meaning |
| --- | --- |
| **No Wi-Fi adapter is currently detected.** | Windows PE sees no wireless adapter: its driver is missing from the media. |
| **No Wi-Fi networks are currently visible.** | No network in range. Select the refresh button above the list. |
| **Hidden network** | A network that does not broadcast its name. |
| **This network requires a provisioned Wi-Fi profile.** | The selected network cannot be joined by typing a passphrase, for example an enterprise network. |
| **No provisioned Wi-Fi profile is available in this boot image.** | The media has Wi-Fi support but no embedded profile. Join a network from the **Wi-Fi** list. |
| **Ready to connect** | The embedded profile is not connected. Foundry Connect tries it by itself while the device has no Internet access. You can also select **Connect**. |
| **Connected** | The device is connected with the embedded profile. |
| **Another Wi-Fi network is active** | The device is connected to a network you joined from the list, not to the embedded profile. |
| **Boot image settings**, **Enterprise profile** | Where the embedded profile comes from: fields typed by the administrator, or an exported profile file. |

</details>

## If you cannot continue

Find the text you see on screen in [Network and Foundry Connect troubleshooting](../troubleshooting/network.md). Do not close Foundry Connect: closing the window cancels the startup.

## Next step

The [startup console](windows-pe-startup.md) downloads Foundry Deploy, then [Foundry Deploy](../foundry-deploy/README.md) opens.
