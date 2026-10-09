# Security and credentials

Foundry deployment media can carry passwords, certificates and tenant information. This page states, for each secret, where it is stored, what protects it, where it ends up on the deployed device and when it is removed.

What **Password protection** covers, and what it does not, is explained on the [General](../foundry-osd/general.md#password-protection) page. The short version: it protects the deployment secrets in the table below, it does not protect network credentials, and it does not encrypt the media as a whole.

## Where each secret is stored

"Protected" means the secret can be decrypted only after the Deployment password is typed in Foundry Deploy. "Recoverable" means anyone who holds the media can decrypt it.

| Secret | On the media | On the deployed device | Removed |
| --- | --- | --- | --- |
| Deployment password | Not stored | Not copied | Not applicable |
| Wi-Fi passwords, 802.1X certificates and their passwords | Recoverable, with or without Password protection: Foundry Connect needs them before any password is asked | Only with network profile roaming: copied to `C:\Windows\Temp\Foundry\Payloads\NetworkProfiles`. A certificate private key and its password are copied, in clear text, only when **Private-key material** shows **Included** | By Foundry, once the profiles are imported at the first start of Windows |
| Local account passwords set in OOBE | Protected. Password protection is required | Written to `C:\Windows\Panther\unattend.xml` in the encoded form Windows expects, which is not encryption | Not removed by Foundry |
| Custom answer files | Protected. Password protection is required | Written in clear text to `C:\Windows\Panther\unattend.xml` | Not removed by Foundry |
| Autopilot JSON profiles | Protected with Password protection, readable without it | Copied to `C:\Windows\Provisioning\Autopilot\AutopilotConfigurationFile.json` | Not removed by Foundry |
| Autopilot certificate (PFX and its password), zero-touch upload | Protected with Password protection, recoverable without it | Never written to disk: Foundry Deploy uses it in memory | Not applicable |
| Device hardware hash | Never on the media | `C:\Windows\Temp\Foundry\Logs\AutopilotHash`, in `AutopilotHWID.csv` and `OA3.xml`. Foundry restricts the folder to SYSTEM and Administrators | Not removed by Foundry |
| Domain Join account and password, zero-touch | Protected. Password protection is required | Copied, in clear text, to `C:\Windows\Temp\Foundry\Payloads\DomainJoin\<operation-id>\credentials.bin`, in a folder restricted to SYSTEM and Administrators | By Foundry, as soon as the join has used the file |
| Domain Join account and password, interactive | Not on the media: the technician types them in Foundry Deploy | Same file as zero-touch | Same as zero-touch |

Interactive hardware hash upload places no secret on the media or on the device: the technician signs in with their own account when the upload runs.

{% hint style="warning" %}
A file listed as "Not removed by Foundry" stays readable to local administrators of the deployed device. If it holds a secret, remove it with your own process once Windows setup has finished, and never attach it to a support request.
{% endhint %}

## How secrets are encrypted on the media

| Item | Value |
| --- | --- |
| Encryption of each secret | AES-256-GCM, with its own 96-bit nonce and a 128-bit authentication tag |
| Deployment key | Random, 256 bits, generated each time media is created |
| With Password protection | The deployment key is itself encrypted with a key derived from the Deployment password. It is not stored in clear on the media |
| Key derivation | PBKDF2 with HMAC-SHA-256, 600,000 iterations, random 128-bit salt |
| Without Password protection | The deployment key is stored on the media, so every deployment secret is recoverable |
| Network secrets | Encrypted with a separate key that is always stored on the media |
| Deployment password length | 8 characters minimum. Foundry OSD warns below 12 |

The Deployment password is never written to the media. Once a technician has typed it, Foundry Deploy can use the secrets for the rest of that session.

## Windows Autopilot credentials

Zero-touch hardware hash upload uses two separate identities. The rights of the first are never available to the media.

| Identity | Used by | Rights |
| --- | --- | --- |
| The administrator, signed in interactively | Foundry OSD, when you connect the tenant | Delegated Microsoft Graph permissions `Application.ReadWrite.All`, `AppRoleAssignment.ReadWrite.All`, `DeviceManagementServiceConfig.Read.All` and `User.Read`, limited by the administrator's own roles and your Conditional Access policies |
| The app registration `Foundry OSD Autopilot Registration` | Foundry Deploy, on the target device | One Microsoft Graph application permission, `DeviceManagementServiceConfig.ReadWrite.All`, used to import the device and read the result |

Facts that follow from this:

- The app registration is single-tenant. When Foundry creates it, it grants only the permission above. When Foundry adopts an existing registration with the same name, it leaves the permissions already there.
- The media carries the tenant ID, the client ID of the app registration and the certificate PFX with its password. It carries no administrator credential and no sign-in token.
- Foundry Deploy signs in as the app registration with the certificate private key. No technician signs in. The certificate is decrypted in memory and is not copied to the deployed device.
- Anyone who obtains the PFX and its password can use the permission of the app registration until the certificate expires or is removed from the registration.
- A certificate created by Foundry OSD is valid for 1, 3, 6 or 12 months. The default is 6 months.
- Foundry OSD does not keep the PFX path or its password between sessions: you select the file and type the password again each time you open the application.

How to create, renew and remove the certificate is described in [Zero-touch hardware hash upload](../foundry-osd/autopilot/zero-touch-hardware-hash.md).

## Domain credentials

During a deployment with Domain Join, Foundry Deploy writes the account and the password of the domain being joined to `credentials.bin` on the target disk, as listed in the table above. Foundry deletes the file as soon as the join has used it. If the deletion fails or the join was interrupted, the Foundry console after the restart shows the cleanup as **Pending**: see [Domain Join troubleshooting](../troubleshooting/domain-join.md).

Diagnostic exports never include a file named `credentials.bin`. A copy saved under another name is not recognized, so do not copy or rename that file. How the join accounts are stored in Foundry OSD is described in [Domain Join](../foundry-osd/domain-join/README.md).

## Recommended practices

- Give the media, and the Deployment password, only to the people who deploy.
- Use dedicated accounts and a dedicated app registration with the minimum rights: a join account that can only join computers, Wi-Fi credentials that can be revoked.
- Prefer a short certificate validity and renew before the expiry date.
- Where your licensing allows it, restrict the `Foundry OSD Autopilot Registration` service principal with [Conditional Access for workload identities](https://learn.microsoft.com/en-us/entra/identity/conditional-access/workload-identity).
- Delete old ISO files and wipe USB drives that are replaced.
- Read logs and screenshots before sharing them: see [Logs and support information](../troubleshooting/logs-and-support.md).

## If media is lost or copied

Treat every secret marked "Recoverable" as disclosed, and every "Protected" secret as disclosed too if the Deployment password may be known.

| Carried by the media | What stops its use |
| --- | --- |
| Wi-Fi password, network certificates | Changing the Wi-Fi password, revoking the certificates |
| Autopilot certificate | Removing the certificate from the app registration |
| Join accounts, local account passwords | Changing those passwords |
| Everything else | Recreating the media with a new Deployment password, and no longer using the old media |
