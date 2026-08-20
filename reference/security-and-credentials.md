# Security and credentials

Foundry deployment media can contain sensitive configuration. Apply the organization’s removable-media, credential, and certificate policies.

## Sensitive material

Depending on configuration, media may include or use:

- Wi-Fi and Ethernet authentication configuration.
- Trusted root certificates.
- Windows Autopilot tenant and application information.
- Certificate-based application credentials.
- Technician password protection.
- Device hardware hashes and registration artifacts.

## Required practices

- Grant access only to authorized administrators and technicians.
- Use dedicated deployment credentials with the minimum required permissions.
- Rotate certificates before expiration and after suspected exposure.
- Revoke credentials when media is lost or cannot be accounted for.
- Sanitize logs, screenshots, and issue attachments.
- Recreate media when a protected-deployment password is lost.

{% hint style="danger" %}
Do not commit credentials, certificates, private keys, tokens, network secrets, or real hardware hashes to the documentation repository.
{% endhint %}
