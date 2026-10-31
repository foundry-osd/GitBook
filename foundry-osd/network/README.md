# Network

The **Network** pages decide how a target device gets online in Windows PE when a cable and DHCP are not enough. Skip them when devices use a wired network with DHCP and no authentication: Foundry Connect needs no configuration for that.

| I want to | Go to |
| --- | --- |
| Authenticate on a wired network that requires 802.1X | [Ethernet 802.1X](ethernet-802.1x.md) |
| Let technicians join a Wi-Fi network in Windows PE | [Wi-Fi](wifi.md) |
| Connect to Wi-Fi without anyone typing a passphrase | [Wi-Fi](wifi.md) |
| Keep the same connection in the installed Windows | [Windows profile roaming](#windows-profile-roaming) |
| Know which hosts the device must reach | [Network endpoints](../../reference/network-endpoints.md) |

## What goes on the media

Everything you configure here is copied to the deployment media: the profile files, the certificate files, the Wi-Fi passphrase and the PFX password.

The Wi-Fi **Passphrase**, the **Client certificate (PFX)** and its **PFX password** are encrypted with the Deployment password, so [Password protection](../general.md#password-protection) is required as soon as you provide one of them. Until it is on, the Wi-Fi or Ethernet 802.1X page shows **Needs attention** with "Wi-Fi passwords and client certificates are protected by the deployment password. Turn on password protection on the General page and set the deployment password.", **Start** shows **Needs attention** and media creation is refused. With Password protection on, an enterprise Wi-Fi profile file that contains key material is encrypted too. Profile files of wired 802.1X and **Trusted root CA certificates** hold no secret and stay readable on the media.

On the target device, Foundry Connect asks the technician for the Deployment password only when it needs one of these secrets. See [Deployment password in Foundry Connect](../../foundry-connect/README.md#deployment-password-in-foundry-connect).

{% hint style="warning" %}
Anyone who holds the media and knows the Deployment password can recover the network secrets, including the `.pfx` file and its password. Use credentials dedicated to deployment that you can revoke. See [Security and credentials](../../reference/security-and-credentials.md).
{% endhint %}

Foundry OSD saves the Wi-Fi **Passphrase**, the **PFX password** and a copy of the files you selected for your Windows account on this PC. The passwords leave this PC only in an export or a publication with **Include passwords**. See [Saved passwords and files](../configuration-sharing/saved-passwords.md). When one is empty, for example after importing a configuration without its passwords, type it again before you create media:

- A missing passphrase blocks **Start** with "Required media secrets are not ready."
- A missing PFX password blocks nothing. The media is created, and the certificate import fails later in Windows PE.

## Windows profile roaming

Roaming copies the network profile used in Windows PE into the installed Windows, so the device can be online when the out-of-box experience (OOBE) starts.

Turn on the **Windows profile roaming** switch on the Ethernet 802.1X page, the Wi-Fi page, or both. The two switches are independent and off by default. Foundry Connect records the profile, Foundry Deploy copies it to the Windows partition, and the post-installation phase imports it at the first start of Windows, before OOBE.

The result depends on the profile:

| Profile | Imported as | Connected before OOBE |
| --- | --- | --- |
| Wi-Fi with **Open**, **OWE** or **WPA2/WPA3 Personal** security | Wi-Fi profile for all users, set to connect automatically | Yes. Foundry also requests the connection. |
| Enterprise Wi-Fi | Wi-Fi profile for all users, unchanged | No. Foundry imports the profile and leaves the connection to Windows. |
| Wired 802.1X | Wired profile, unchanged | Only when Windows holds the computer credential that the profile requires. |

Certificates follow their profile:

| File on the media | Store in the installed Windows | When |
| --- | --- | --- |
| Root CA certificate (`.cer`, `.crt`) | Trusted root certification authorities of the local computer | Always, when roaming is on for that connection |
| Client certificate (`.pfx`) | Personal store of the local computer, with its private key | Only with **Include private-key certificate material** |

Turn on **Include private-key certificate material** only when Windows must keep authenticating with that certificate after deployment, and plan its renewal and revocation.

Which Wi-Fi networks are roamed:

- Every Wi-Fi network that the device joined successfully in Foundry Connect, the embedded profile as well as a network the technician joined by typing a password. This applies even when the cable stays connected and Ethernet carries the Internet access.
- When several qualify, the one that carries the Internet access when Foundry Connect closes is preferred in the installed Windows.
- A network that failed to connect, or that the device never joined during the session, is not roamed.

Leave Wi-Fi roaming off when a password typed by a technician must not stay on the device.

The wired 802.1X profile is roamed only when Foundry Connect applied it. It is not roamed when the **802.1X** row of Foundry Connect shows **Unavailable**, for example after the technician cancelled the Deployment password, or when the profile was missing from the media or refused by Windows PE.

A failed import does not stop the deployment. See [The roamed profile does not connect after deployment](../../troubleshooting/network.md#roamed-profile-does-not-connect).

## Before you create media

- Add the Windows PE drivers that the network adapters need. See [General](../general.md).
- Check that the deployment network reaches the hosts listed in [Network endpoints](../../reference/network-endpoints.md).
- Check that certificates stay valid for as long as the media is used. On a network that requires authentication, Windows PE can correct the device clock only after **Network ready**, so a device with a wrong firmware clock can fail certificate authentication.
- Test the media on one device of each model, on the production switch port or SSID, before you distribute it.

## Related

- [Network readiness in Foundry Connect](../../foundry-connect/network-readiness.md)
- [Network and Foundry Connect troubleshooting](../../troubleshooting/network.md)
- [Security and credentials](../../reference/security-and-credentials.md)
