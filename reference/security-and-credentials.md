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
- Custom Windows answer files, including credentials in their settings or commands.
- Device hardware hashes and registration artifacts.
- Active Directory join credentials for Domain Join (unreleased).

## Required practices

- Grant access only to authorized administrators and technicians.
- Use dedicated deployment credentials with the minimum required permissions.
- Rotate certificates before expiration and after suspected exposure.
- Revoke credentials when media is lost or cannot be accounted for.
- Sanitize logs, screenshots, and issue attachments.
- Recreate media when a Protected deployment password is lost.

## Deployment media protection

Review the [Protected deployment scope](../foundry-osd/general.md#protected-deployment) before choosing credentials and files to include on media. The technician password protects the listed deployment data; embedded network credentials remain accessible to anyone who can read the ISO or USB.

- Every retained [Autopilot JSON profile](../foundry-osd/autopilot/json-profile.md) is readable on media created without Protected deployment.
- [Zero-touch upload](../foundry-osd/autopilot/zero-touch-hardware-hash.md) credentials can be recovered from media created without Protected deployment.
- Non-empty [OOBE local account passwords](../foundry-osd/customization/oobe.md#password-protection) require Protected deployment under Foundry's security policy and are encrypted in the deployment configuration. During Windows Setup, they are written to `unattend.xml` using reversible encoding, not encryption. Treat that answer file and its copies as sensitive data.
- Protected deployment does not encrypt the complete ISO, USB drive, Windows image, or data staged into the installed Windows system.
- [Zero Touch Domain Join (unreleased)](../foundry-osd/domain-join/zero-touch.md) requires the existing General protection key and Deploy unlock session. [Interactive mode](../foundry-osd/domain-join/interactive.md) collects credentials at launch and adds no media-password prerequisite.

If media is lost, stolen, or copied without authorization, revoke embedded credentials where applicable and recreate the media. Do not rely on the technician password as a substitute for physical media controls.

## Domain credentials (unreleased)

Domain passwords are context-bound to the active domain/account. Local **Remember passwords** and encrypted shared revisions can retain them under their respective choices. Ordinary portable exports always omit the direct domain password, including when confidential inputs are selected. Connection/recovery files also omit it, but shared-access keys can grant access to secrets in revisions. Follow [profile retention and sharing rules](../foundry-osd/deployment-profiles.md#domain-credentials-unreleased).

Deploy stages a temporary plaintext binary payload at `%SystemRoot%\Temp\Foundry\Payloads\DomainJoin\<operation-id>\credentials.bin`. The operation directory has protected inheritance and permits SYSTEM and Administrators before the file is created. Joining is its only sensitive consumer; later membership verification uses no join credentials. Target access controls and physical security remain necessary.

Foundry zeroes its owned encoded/decoded buffers and scoped unmanaged native-password buffer when their use ends, and disposes its controlled temporary credential objects. The standard LDAP library can create immutable library-owned password copies, and Windows authentication has its own memory. Complete erasure of managed/system authentication memory is not guaranteed. The library connection/credential lifetime is bounded by the child operation/process; the worker has a 300-second budget, with read-only readiness limited to 120 seconds within it. Cancellation does not prove that a synchronous mutation has stopped.

Safe cleanup makes at most three deletion attempts per immediate/final gate, 250 ms apart. A potentially active same-boot worker defers deletion until a safe later boot. A domain-only deletion failure remains **Pending** with a warning through subsequent actions and finalization; it does not relax unrelated sensitive-cleanup or journal-integrity failures. An administrator must confirm that no active worker remains before inspecting or removing a leftover credential file. Preserve safe results/journal/logs, and never copy the payload into support evidence. See [domain cleanup troubleshooting](../troubleshooting/domain-join.md#credential-cleanup-remains-pending).

This payload's disposal is separate from Panther XML cleanup. A known `credentials.bin` filename is excluded from sanitized and raw support bundles before reading, but this is not a promise about arbitrarily renamed files or unlabelled secrets.

## Custom answer files

Using [custom answer files](../foundry-osd/customization/unattend.md) requires Protected deployment for every embedded file. The complete XML is encrypted on media. Source files and the decrypted `Windows\Panther\unattend.xml` on the target still require access controls.

Custom XML may contain passwords or secrets in commands and extensions. Do not assume Windows will scrub them. Keep the target file until its required setup passes finish, then arrange cleanup through the deployment process. Do not include raw answer files in support attachments.

{% hint style="danger" %}
Do not commit credentials, certificates, private keys, tokens, network secrets, or real hardware hashes to the documentation repository.
{% endhint %}
