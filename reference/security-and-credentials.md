# Security and credentials

Foundry deployment media can contain sensitive configuration. Apply the organization’s removable-media, credential, and certificate policies.

## Sensitive material

Depending on configuration, media may include or use:

- Wi-Fi and Ethernet authentication configuration.
- Trusted root certificates.
- Windows Autopilot tenant and application information.
- Certificate-based application credentials.
- Protected deployment and its technician password.
- Predefined passwords for local Windows accounts created during OOBE.
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
- Non-empty [OOBE local account passwords](../foundry-osd/customization/oobe.md#password-protection) require Protected deployment under Foundry's security policy and are encrypted in the deployment configuration. During Windows Setup, they are written to `unattend.xml` using reversible encoding, not encryption. Treat that answer file and its copies as sensitive data.
- Protected deployment does not encrypt the complete ISO, USB drive, Windows image, or data staged into the installed Windows system.

If media is lost, stolen, or copied without authorization, revoke embedded credentials where applicable and recreate the media. Do not rely on the technician password as a substitute for physical media controls.

## Custom answer files (unreleased)

The [custom answer-file feature](../foundry-osd/customization/unattend.md) in Foundry PR #314 requires Protected deployment for every embedded file. The complete XML is encrypted on media. Source files and the decrypted `Windows\Panther\unattend.xml` on the target still require access controls.

Custom XML may contain passwords or secrets in commands and extensions. Do not assume Windows will scrub them. Keep the target file until its required setup passes finish, then arrange cleanup through the deployment process. Do not include raw answer files in support attachments.

{% hint style="danger" %}
Do not commit credentials, certificates, private keys, tokens, network secrets, or real hardware hashes to the documentation repository.
{% endhint %}
