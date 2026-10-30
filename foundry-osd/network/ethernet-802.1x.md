# Ethernet 802.1X

Use this page when the wired deployment network only admits devices that authenticate with 802.1X. Foundry puts a wired profile, and optionally certificates, on the deployment media. Foundry Connect applies them at startup. With a client certificate, it first asks the technician for the Deployment password.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-network-ethernet-01-profile-configuration.png" alt="Ethernet 802.1X page enabled with a profile template file, Use certificates turned on, the Client certificate (PFX) card with its PFX password box, the Trusted root CA certificates list, and Windows profile roaming expanded">
  <figcaption>The profile template, the client certificate with its password, the trusted root CA certificates, and Windows profile roaming with its private-key option. The PFX password and the private-key option only matter for a client certificate.</figcaption>
</figure>

## Before you start

- **A wired profile exported as XML.** Foundry does not build the profile: the EAP method, the server validation and the credential source all come from this file. Export it on a Windows computer that already authenticates on this network:

  ```
  netsh lan show profiles
  netsh lan export profile folder="C:\Temp" interface="Ethernet"
  ```

  Replace `Ethernet` with the interface name shown by the first command. The second command writes one `.xml` file into the folder.
- **A profile that authenticates the device by itself.** Nothing in Windows PE can ask for a user name and password. A profile that expects typed credentials cannot authenticate. Use computer authentication, typically with a certificate (EAP-TLS).
- **The certificates, if the profile needs them**: for EAP-TLS, a client certificate with its private key (`.pfx` or `.p12`) and its password, plus the root CA certificate (`.cer` or `.crt`) that issued the certificate of the authentication server.
- **Password protection, if you use a client certificate.** Turn on [Password protection](../general.md#password-protection) on **General** and set the **Deployment password**.

## Configure wired 802.1X

1. Open **Network > Ethernet 802.1X**.
2. Turn on the switch at the top of the page, next to **Documentation**. It shows **Enabled** and unlocks the fields.
3. In **Profile template file**, select **Browse** and choose the exported `.xml` file.
4. If the profile needs certificates, turn on **Use certificates**. The **Client certificate (PFX)** and **Trusted root CA certificates** cards appear.
5. In **Client certificate (PFX)**, select **Browse**, choose the `.pfx` or `.p12` file and type its password in **PFX password**. **Remove** clears it.
6. In **Trusted root CA certificates**, select **Add certificate** for each `.cer` or `.crt` file. To take one out, select it in the list, then **Remove**.
7. To keep the connection in the installed Windows, turn on **Windows profile roaming**. See [Windows profile roaming](README.md#windows-profile-roaming) for what is imported and when.

| Card | What happens in Windows PE |
| --- | --- |
| **Client certificate (PFX)** | The client certificate and its private key are imported into the personal store of the local computer, using **PFX password**. |
| **Trusted root CA certificates** | Each certificate is added to the trusted root certification authorities. |

With **Use certificates** on, at least one of the two cards must hold a certificate: otherwise the page shows "Wired 802.1X requires a client certificate or a trusted root CA certificate when certificates are used."

A client certificate requires Password protection. Without it, the page shows **Needs attention** with "Wi-Fi passwords and client certificates are protected by the deployment password. Turn on password protection on the General page and set the deployment password.", and media creation is refused.

When you open a configuration that holds a single certificate file, Foundry OSD shows it under the matching card: a `.pfx` or `.p12` file under **Client certificate (PFX)**, with its password, and any other file under **Trusted root CA certificates**.

## What the technician sees

When it starts, Foundry Connect imports the certificates, adds the wired profile and asks every Ethernet adapter that has a cable connected to reconnect. Without a client certificate, the technician has nothing to do.

With a client certificate, Foundry Connect first asks for the Deployment password in a **Protected network settings** window. Once the password is accepted, wired 802.1X is applied, and Foundry Deploy does not ask for the password again. If the technician selects **Cancel**, wired 802.1X stays **Unavailable** for the session: plain Ethernet and Wi-Fi keep working. See [Deployment password in Foundry Connect](../../foundry-connect/README.md#deployment-password-in-foundry-connect).

The **Ethernet** card of Foundry Connect has an **802.1X** row:

| On screen | Meaning |
| --- | --- |
| **Authenticating** | The network is checking the device. |
| **Authenticated** | The network accepted the device. The card then shows **Connected** once the address arrives. |
| **Profile applied** | The profile is in place, but Foundry Connect cannot read the result. An address under **IPv4** means that it worked. |
| **Authentication failed** | The profile is missing from the media, or the profile or the network refused the device. A line under **802.1X** gives a short reason. |
| **Unavailable** | The profile is not used. A line under **802.1X** gives the reason: the Deployment password was not entered, or a secret of the profile cannot be read from this media and the line says to recreate it. A port without 802.1X still works. |

See ["Authentication failed"](../../troubleshooting/network.md#wired-authentication-fails) when the device is refused.

## Check the result

- In Foundry OSD, **Start** does not report "Network configuration is not ready."
- On a device connected to a production switch port, the **Ethernet** card in Foundry Connect shows **Connected** and **802.1X** shows **Authenticated** or **Profile applied**, then the status banner shows **Network ready**.

## Limits

- One profile file, one client certificate and any number of trusted root CA certificates. From a `.pfx` file, Foundry Connect imports the client certificate only: add its issuing CA under **Trusted root CA certificates**.
- Foundry OSD checks that the files exist. It does not check the content of the profile or the PFX password. A mistake shows only in Windows PE.
- The client certificate and its **PFX password** are protected by the Deployment password; the profile file and the trusted root CA certificates are readable on the media. Foundry OSD keeps the **PFX password** between sessions only while **Remember passwords** is on. See [What goes on the media](README.md#what-goes-on-the-media).
- On protected media, the client certificate file must not be empty and must stay under about 3 MB. Otherwise media creation stops with an error.

## Related

- [Network](README.md)
- [Network readiness in Foundry Connect](../../foundry-connect/network-readiness.md)
- [Network and Foundry Connect troubleshooting](../../troubleshooting/network.md)
