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
- Active Directory join credentials for Domain Join.

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
- [Zero-touch Domain Join](../foundry-osd/domain-join/zero-touch.md) requires Protected deployment; its accounts are unlocked with the technician password. [Interactive mode](../foundry-osd/domain-join/interactive.md) collects the account and password in the Deploy wizard and does not require Protected deployment.

If media is lost, stolen, or copied without authorization, revoke embedded credentials where applicable and recreate the media. Do not rely on the technician password as a substitute for physical media controls.

## Domain credentials

In Foundry OSD, a join password belongs to its account, shared or dedicated. On Zero-touch media, each domain has its own encrypted copy of its account and password, which cannot be used to join another domain. **Remember passwords** and encrypted shared revisions can keep the passwords when you choose so; a `.foundryprofile` export never contains them. Connection and recovery files do not contain them either, but they give access to shared revisions that can. See [domain credentials in profiles](../foundry-osd/deployment-profiles.md#domain-credentials).

During deployment, Deploy copies the account and password of the domain being joined to the target disk, unencrypted, in `%SystemRoot%\Temp\Foundry\Payloads\DomainJoin\<operation-id>\credentials.bin`. The folder is restricted to SYSTEM and Administrators before the file is written. Only the join reads it; the later membership check uses no credentials. Access to the disk and the physical security of the computer remain your responsibility until the file is deleted.

Foundry erases its own copies of the password from memory as soon as it has used them. The Windows components that perform the join keep their own copies during the operation, which Foundry cannot erase. The join is limited to five minutes, including up to two minutes waiting for a domain controller.

Foundry deletes the file as soon as the join has used it. If the deletion fails, or if the join was interrupted, the cleanup stays **Pending** with a warning and is tried again at the next start. An administrator can delete a leftover file once the computer is no longer running Foundry actions. Never copy it into a support request. See [credential cleanup](../troubleshooting/domain-join.md#credential-cleanup-remains-pending).

Support bundles never include a file named `credentials.bin`. A copy saved under another name would not be recognized, so do not copy or rename this file.

## Custom answer files

Using [custom answer files](../foundry-osd/customization/unattend.md) requires Protected deployment for every embedded file. The complete XML is encrypted on media. Source files and the decrypted `Windows\Panther\unattend.xml` on the target still require access controls.

Custom XML may contain passwords or secrets in commands and extensions. Do not assume Windows will scrub them. Keep the target file until its required setup passes finish, then arrange cleanup through the deployment process. Do not include raw answer files in support attachments.

{% hint style="danger" %}
Do not commit credentials, certificates, private keys, tokens, network secrets, or real hardware hashes to the documentation repository.
{% endhint %}
