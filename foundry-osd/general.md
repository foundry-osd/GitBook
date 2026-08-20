# General configuration

Use General configuration to define deployment behavior shared by the generated media.

## Platform and Windows PE

Configure:

- Target architecture.
- Secure Boot signing compatibility, including CA 2023 when required by the deployment environment.
- Windows PE language.
- Deployment time zone.

The selected Windows PE language remains unavailable until Windows ADK and Windows PE Add-on `10.1.26100.2454` are ready.

## Deployment completion

Choose whether Foundry Deploy reboots automatically after success and configure the displayed reboot delay when automatic reboot is enabled.

## Drivers

Enable the supported Dell or HP driver options required by the hardware fleet. Add a custom driver directory when Windows PE needs network or storage drivers that are not provided by the selected vendor options.

Validate custom drivers on representative hardware before using the media in production.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-general-01-overview.png`
- **Capture:** Show the General configuration page with its major setting groups.
{% endhint %}

## Protected deployment

Protected deployment requires a technician password before Foundry Deploy initializes.

{% hint style="warning" %}
The technician password cannot be recovered from generated media. If it is lost, recreate the media with a new password.
{% endhint %}

Store the password using the organization’s approved credential-management process. Do not include it in documentation, issue reports, screenshots, or deployment notes.

## Review readiness

Return to **Start** after saving the required settings. General configuration appears in the readiness summary and must be valid before media creation begins.
