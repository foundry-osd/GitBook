# Ethernet 802.1X

Use this page when the wired deployment network only admits devices that authenticate with 802.1X. Foundry puts a wired profile, and optionally a certificate, on the deployment media. Foundry Connect applies them at startup without asking the technician anything.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-network-ethernet-01-profile-configuration.png`
- **Capture:** Show the Ethernet 802.1X page enabled, with a **Profile template file** selected, **Import a trusted root CA certificate** turned on, the **Trusted root CA certificate file** path with its **PFX password** box, and **Windows profile roaming** expanded. Use demonstration file names.
{% endhint %}

## Before you start

- **A wired profile exported as XML.** Foundry does not build the profile: the EAP method, the server validation and the credential source all come from this file. Export it on a Windows computer that already authenticates on this network:

  ```
  netsh lan show profiles
  netsh lan export profile folder="C:\Temp" interface="Ethernet"
  ```

  Replace `Ethernet` with the interface name shown by the first command. The second command writes one `.xml` file into the folder.
- **A profile that authenticates the device by itself.** Nothing in Windows PE can ask for a user name and password. A profile that expects typed credentials cannot authenticate. Use computer authentication, typically with a certificate (EAP-TLS).
- **The certificate file, if the profile needs one**: a root CA certificate (`.cer` or `.crt`), or a client certificate with its private key (`.pfx`) and its password.

## Configure wired 802.1X

1. Open **Network > Ethernet 802.1X**.
2. Turn on the switch at the top of the page, next to **Documentation**. It shows **Enabled** and unlocks the fields.
3. In **Profile template file**, select **Browse** and choose the exported `.xml` file.
4. If the profile needs a certificate, turn on **Import a trusted root CA certificate**. In **Trusted root CA certificate file**, select **Browse** and choose the file.
5. For a `.pfx` file, type its password in **PFX password**. Leave the box empty for a `.cer` or `.crt` file.
6. To keep the connection in the installed Windows, turn on **Windows profile roaming**. See [Windows profile roaming](README.md#windows-profile-roaming) for what is imported and when.

The certificate field has one label for two uses. The file type decides what Foundry Connect does with it:

| File you select | What happens in Windows PE |
| --- | --- |
| `.cer` or `.crt` | The certificate is added to the trusted root certification authorities. |
| `.pfx` or `.p12` | The client certificate and its private key are imported into the personal store of the local computer, using **PFX password**. |

## What the technician sees

Nothing to do. When it starts, Foundry Connect imports the certificate, adds the wired profile and asks the first Ethernet adapter to reconnect. When authentication succeeds, the **Ethernet** card shows **Connected**.

When authentication fails, no error is shown: the **Ethernet** card simply does not get a usable address. It may stay on **Waiting for network configuration**, or show **Connected** with an address that starts with `169.254`. See [Wired 802.1X does not authenticate](../../troubleshooting/network.md#wired-authentication-fails).

## Check the result

- In Foundry OSD, **Start** does not report "Network configuration is not ready."
- On a device connected to a production switch port, the **Ethernet** card in Foundry Connect shows **Connected**, then the header shows **Network ready**.

## Limits

- One profile file and one certificate file. You cannot supply both a root CA file and a client `.pfx` file. From a `.pfx` file, Foundry Connect imports the client certificate only: the issuing CA is not added to the trusted roots.
- Foundry OSD checks that the files exist. It does not check the content of the profile or the PFX password. A mistake shows only in Windows PE.
- Foundry OSD does not keep the **PFX password** when you close it, and the files are readable on the media. See [What goes on the media](README.md#what-goes-on-the-media).

## Related

- [Network](README.md)
- [Network readiness in Foundry Connect](../../foundry-connect/network-readiness.md)
- [Network and Foundry Connect troubleshooting](../../troubleshooting/network.md)
