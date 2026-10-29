# Network readiness

Foundry Connect shows the state of the wired and wireless connections of the target device, and lets you continue once it has reached the Internet.

<figure>
  <img src="../.gitbook/assets/foundry-connect-network-readiness-01-ready.png" alt="Foundry Connect showing Network ready with Ethernet as the connection, the Continue and Stay here buttons, the Continuing automatically in 10s countdown, and the Ethernet card folded on Connected">
  <figcaption>Once Internet access is validated, <strong>Continue</strong> closes Foundry Connect and the startup continues to Foundry Deploy. <strong>Stay here</strong> stops the countdown.</figcaption>
</figure>

## Join a Wi-Fi network

With a cable and DHCP, or a profile from the media, you have nothing to do. To join a Wi-Fi network yourself:

1. In the **Wi-Fi** card, select the network in the list. Use the refresh button next to the card title if it is missing.
2. Type its password in **Wi-Fi password**. The eye button shows what you typed.
3. Select **Connect**, or press Enter in the password box. **Connect** stays unavailable until you type a password, except on an open or Enhanced Open network.

<figure>
  <img src="../.gitbook/assets/foundry-connect-network-readiness-02-wifi-password.png" alt="Wi-Fi card with a WPA2 Personal network selected in the list and, next to it, the Wi-Fi password box, its eye button and the Connect button">
  <figcaption>The details of the selected network sit next to the list on a wide window, and under it on a small screen.</figcaption>
</figure>

With the keyboard only, Tab moves into the list, the arrow keys choose a network, and Tab again reaches the password box and **Connect**. The list keeps its order while a network is selected, so a new scan never moves the row you are on.

The **Wi-Fi** card appears only when the media was created with Wi-Fi enabled. You can join open, Enhanced Open, WPA2 Personal and WPA3 Personal networks. The password has 8 to 63 characters. Other networks are listed but cannot be joined from here:

- An enterprise network needs a profile from the media: "This network requires a provisioned Wi-Fi profile."
- A WEP or WPA Personal network is refused: "This Wi-Fi network is not supported in this build."
- A network that hides its name is not listed at all. To use one, the administrator puts its profile on the media.

When [Windows profile roaming](../foundry-osd/network/README.md#windows-profile-roaming) is on for Wi-Fi, every network you join successfully here is also set up in the installed Windows, even if the cable stays connected. Type only a password that may stay on the device.

## Use the provisioned Wi-Fi

The **Provisioned Wi-Fi** card shows the profile that the administrator put on the media, with its **Profile** name and **Authentication**.

While the device has no Internet access, Foundry Connect connects this profile by itself, and tries again about every 20 seconds at first, then less and less often, up to every 2 minutes. A working cable is used first: while Ethernet gives Internet access, Foundry Connect does not try the profile.

As soon as you connect or disconnect a network yourself, Foundry Connect stops trying the provisioned profile for the rest of the session, so it never takes back a connection you chose. The **Connect** button of the card stays available: select it to use the profile again.

When the profile cannot be used from this media, the card shows **Provisioned Wi-Fi unavailable** and the reason. Ethernet and the other Wi-Fi networks keep working. See ["Provisioned Wi-Fi unavailable"](../troubleshooting/network.md#provisioned-wifi-unavailable).

<!-- TODO lot 4: explain that the provisioned Wi-Fi can ask for the Deployment password before it connects, and what happens when the technician cancels. -->

## What Network ready tests

Every 10 seconds, and when you select **Retry** or **Tools > Refresh status**, Foundry Connect asks two Microsoft test addresses, `www.msftconnecttest.com` and `www.msftncsi.com`, for a known answer. **Network ready** appears only when one of them sends exactly that answer within 5 seconds.

Any other answer means that something on the network intercepts web traffic, such as a sign-in page or a proxy that asks for credentials. Foundry Connect then shows "Sign-in page detected" and stays on **No internet access**: Windows PE has no browser to sign in.

The test cannot be changed or skipped. **Network ready** does not prove that the other hosts in [Network endpoints](../reference/network-endpoints.md) are reachable.

## What the statuses mean

| On screen | Meaning | What to do |
| --- | --- | --- |
| **Checking the network** | Foundry Connect has just opened. | Wait a few seconds. |
| **Connecting**, "Applying the network settings from the boot media." | Foundry Connect is installing the profiles and certificates from the media. | Wait. |
| **No internet access** and a reason | The device is not online yet. | Read the reason, then see [Network and Foundry Connect troubleshooting](../troubleshooting/network.md). |
| **Network ready**, "Internet access has been validated. You can continue now." | A test address answered. | Select **Continue** or wait. |
| **Ethernet**, **Wi-Fi ·** name or **Provisioned Wi-Fi ·** name under the status | The connection in use. | Nothing. |

<details>

<summary>Ethernet card</summary>

| On screen | Meaning |
| --- | --- |
| "No Ethernet adapter detected." | Windows PE sees no wired adapter: missing driver, or adapter disabled in the firmware. The line replaces the card. |
| **No active link**, "Check the cable connection" | The adapter has no link: cable, dock or switch port. |
| **Waiting for network configuration**, "Waiting for DHCP or static network configuration" | The link is up but the adapter has no address yet. |
| **Waiting for network configuration**, "Self-assigned address: the DHCP server did not answer" | Windows gave the adapter an address itself because no DHCP server answered. |
| **Connected**, "DHCP lease detected" or "Static network configuration detected" | The adapter has a usable address. |
| **Adapter**, **IPv4**, **Gateway** | The details of the wired adapter. **Unavailable** when there is nothing to show. |
| **802.1X**: **Authenticating** | The media carries a wired 802.1X profile and the network is checking the device. |
| **802.1X**: **Authenticated** | The network accepted the device. |
| **802.1X**: **Profile applied** | The profile is in place, but the result of the authentication cannot be read. An address under **IPv4** means it worked. |
| **802.1X**: **Authentication failed** | The profile or the network refused the device. A box under the details gives the reason. |
| **802.1X**: **Unavailable** | The profile cannot be used from this media. A box under the details gives the reason. |

</details>

<details>

<summary>Wi-Fi and Provisioned Wi-Fi cards</summary>

| On screen | Meaning |
| --- | --- |
| "No Wi-Fi adapter is currently detected." | Windows PE sees no wireless adapter. The line replaces the Wi-Fi cards. |
| "Wi-Fi support is not available in this boot environment." | The wireless service of Windows PE is not running. The line replaces the Wi-Fi cards. |
| "No Wi-Fi networks are currently visible." | No network in range. Select the refresh button next to the card title. |
| "Select a network in the list to connect." | Nothing is selected yet. |
| A dimmed network in the list | The selected network is no longer seen by the adapter. It stays selected but cannot be connected. |
| **Not in range** | The provisioned network is not seen by the adapter. |
| **Ready to connect** | The provisioned profile is not connected yet. |
| **Connected** | The device uses this network. |
| **Another Wi-Fi network is active** | The device uses a network you joined from the list, not the provisioned profile. This is not an error. |
| **Open**, **Enhanced Open**, **WPA2 Personal**, **WPA3 Personal**, **WPA2/WPA3 Personal**, **Enterprise** | The security of the network or of the provisioned profile. **Configured security** means a profile whose security is not shown. |

</details>

## If you cannot continue

Find the text you see on screen in [Network and Foundry Connect troubleshooting](../troubleshooting/network.md).

## Next step

The [startup console](windows-pe-startup.md) prepares Foundry Deploy, then [Foundry Deploy](../foundry-deploy/README.md) opens.
