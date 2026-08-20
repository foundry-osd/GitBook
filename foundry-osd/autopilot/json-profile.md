# JSON profile

Use a local Autopilot JSON profile when the deployment should stage profile settings without uploading the device hardware hash.

## Configure the profile

1. Open **Windows Autopilot > JSON profiles**.
2. Import a supported local profile or connect to the tenant and download an available profile.
3. Review the profile information displayed by Foundry OSD.
4. Select the profile that should be included in deployment media.
5. Return to **Start** and confirm Autopilot readiness.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-autopilot-json-profile-01-import.png`
- **Capture:** Show the JSON profile import and selection controls without tenant-specific data.
{% endhint %}

## Expected result

Foundry stages the selected profile for the deployed Windows installation. Hardware registration remains a separate administrative responsibility.

## Replace a profile

Import the updated profile, select it, and recreate or update the deployment media. Remove profiles that are no longer approved for deployment.
