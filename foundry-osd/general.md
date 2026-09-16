# General configuration

Use General configuration to define deployment behavior shared by the generated media.

## Platform and Windows PE

Configure:

- Target architecture.
- Secure Boot signing compatibility, including CA 2023 when required by the deployment environment.
- Windows PE language.
- Windows PE time zone.

The selected Windows PE language remains unavailable until Windows ADK and Windows PE Add-on `10.1.26100.2454` are ready.

### Windows PE time zone

Keep **Automatic** to detect the time zone from the deployment network's public IP address after Foundry Connect establishes connectivity. Windows PE uses **UTC** if detection is unavailable or the result cannot be mapped to a supported time zone.

Select a specific time zone to override automatic detection, for example when the network's public IP location differs from the deployment site. This setting applies only to the Windows PE session. Recreate or update the media after changing it.

## Deployment completion

Choose whether Foundry Deploy reboots automatically after success and configure the displayed reboot delay when automatic reboot is enabled.

## Drivers

Enable the supported Dell or HP driver options required by the hardware fleet. Add a custom driver directory when Windows PE needs network or storage drivers that are not provided by the selected vendor options.

Validate custom drivers on representative hardware before using the media in production.

<figure>
  <img src="../.gitbook/assets/foundry-osd-general-01-overview.png" alt="Foundry OSD General configuration page">
  <figcaption>Configure platform, Windows PE, deployment completion, and driver settings.</figcaption>
</figure>

## Protected deployment

**Protected deployment** requires a technician password before Foundry Deploy initializes. Enable it to protect the following deployment data on generated media:

| Data | Protected by the technician password |
| --- | --- |
| OOBE local account passwords | Yes |
| Autopilot certificate credentials for zero-touch upload, including the PFX file and its password | Yes |
| Autopilot JSON profiles | Yes |
| Custom Windows answer files | Yes |
| Embedded Wi-Fi passwords, wired and Wi-Fi certificate PFX passwords, and network certificate private keys | No |

Foundry Connect uses embedded network credentials before Foundry Deploy asks for the technician password, so automatic network setup remains available. Anyone who can read the ISO or USB can recover those network credentials, even when Protected deployment is enabled. Restrict access to the media and use dedicated network credentials that can be revoked.

Foundry accepts passwords from 8 characters and recommends at least 12 characters. Use a unique password for each set of deployment media and store it using the organization’s approved credential-management process.

Protected deployment does not encrypt the complete ISO, USB drive, Windows image, or files staged into the installed Windows system.

When Protected deployment is disabled, Autopilot JSON profiles remain readable on the media and other embedded deployment credentials can be recovered without a technician password. Treat possession of unprotected media as access to all embedded deployment information.

{% hint style="warning" %}
If you no longer have the technician password, recreate the media with a new password.
{% endhint %}

Do not include the password in documentation, issue reports, screenshots, or deployment notes.

## Review readiness

Return to **Start** after saving the required settings. General configuration appears in the readiness summary and must be valid before media creation begins.
