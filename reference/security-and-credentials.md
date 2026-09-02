# Security and credentials

Foundry deployment media can contain sensitive configuration. Apply the organization’s removable-media, credential, and certificate policies.

## Sensitive material

Depending on configuration, media may include or use:

- Wi-Fi and Ethernet authentication configuration.
- Trusted root certificates.
- Windows Autopilot tenant and application information.
- Certificate-based application credentials.
- Protected deployment and its technician password.
- Device hardware hashes and registration artifacts.

## Required practices

- Grant access only to authorized administrators and technicians.
- Use dedicated deployment credentials with the minimum required permissions.
- Rotate certificates before expiration and after suspected exposure.
- Revoke credentials when media is lost or cannot be accounted for.
- Sanitize logs, screenshots, and issue attachments.
- Recreate media when a Protected deployment password is lost.

## Deployment media protection

Use [Protected deployment](../foundry-osd/general.md#protected-deployment) when media contains credentials, private keys, or Autopilot JSON profiles that must not remain directly accessible.

- Every retained [Autopilot JSON profile](../foundry-osd/autopilot/json-profile.md) is readable on media created without Protected deployment.
- [Zero-touch upload](../foundry-osd/autopilot/zero-touch-hardware-hash.md) credentials remain encrypted without Protected deployment, but the deployment key required to decrypt them is stored on the same media.
- Protected deployment does not encrypt the complete ISO, USB drive, Windows image, or data staged into the installed Windows system.

If media is lost, stolen, or copied without authorization, revoke embedded credentials where applicable and recreate the media. Do not rely on the technician password as a substitute for physical media controls.

{% hint style="danger" %}
Do not commit credentials, certificates, private keys, tokens, network secrets, or real hardware hashes to the documentation repository.
{% endhint %}
