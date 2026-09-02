# JSON profile

Use a local Autopilot JSON profile when the deployment should stage profile settings without uploading the device hardware hash.

## Configure the profile

1. Open **Windows Autopilot > JSON profiles**.
2. Import a supported local profile or connect to the tenant and download an available profile.
3. Review the profile information displayed by Foundry OSD.
4. Select the profile that Foundry Deploy should use by default.
5. Return to **Start** and confirm Autopilot readiness.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-autopilot-json-profile-01-import.png" alt="Foundry OSD Windows Autopilot JSON profile import and selection controls">
  <figcaption>Import or download profiles, then select the default profile for deployment.</figcaption>
</figure>

All profiles retained in Foundry OSD are included in media created for the JSON profile workflow. The selected profile is the default shown in Foundry Deploy; it does not limit which profiles are copied. Remove every profile that should not be distributed with the media.

## Protect the profile on deployment media

Enable **Protected deployment** from [General configuration](../general.md) before creating the media when the retained Autopilot profiles must not remain readable on the ISO or USB drive.

When protection is enabled, Foundry stores every retained profile on the deployment media using AES-256-GCM encryption. Readable JSON files are not stored alongside the encrypted profiles. Foundry Deploy asks for the technician password, unlocks the deployment key, and decrypts the selected profile before staging it in the Windows installation.

{% hint style="warning" %}
Without Protected deployment, every retained Autopilot JSON profile is stored in readable form on the deployment media. Restrict access to the ISO or USB drive and recreate the media if it is lost or copied without authorization.
{% endhint %}

## Expected result

Foundry stages the profile selected in Foundry Deploy for the Windows installation. Hardware registration remains a separate administrative responsibility.

## Replace a profile

Import the updated profile, select it, and recreate or update the deployment media. Remove every obsolete or unapproved profile before creating media because all retained profiles are included.
