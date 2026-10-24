# Security and credentials

Foundry deployment media can carry passwords, certificates and tenant information. This page states, for each secret, where it is stored, what protects it, where it ends up on the deployed device and when it is removed.

What **Password protection** covers, and what it does not, is explained on the [General](../foundry-osd/general.md#password-protection) page. The short version: it protects the deployment secrets in the table below, it does not protect network credentials, and it does not encrypt the media as a whole.

## Where each secret is stored

"Protected" means the secret can be decrypted only after the Deployment password is typed in Foundry Deploy. "Recoverable" means anyone who holds the media can decrypt it.

| Secret | On the media | On the deployed device | Removed |
| --- | --- | --- | --- |
| Deployment password | Not stored | Not copied | Not applicable |
| Wi-Fi passwords, 802.1X certificates and their passwords | Recoverable, with or without Password protection | Only with network profile roaming: `C:\Windows\Temp\Foundry\Payloads\NetworkProfiles` | By Foundry, once the profiles are imported at the first start of Windows |
| Local account passwords set in OOBE | Protected. Password protection is required | `C:\Windows\Panther\unattend.xml`, encoded but not encrypted | Not removed by Foundry |
| Custom answer files | Protected. Password protection is required | `C:\Windows\Panther\unattend.xml`, in clear text | Not removed by Foundry |
| Autopilot JSON profiles | Protected with Password protection, readable without it | `C:\Windows\Provisioning\Autopilot\AutopilotConfigurationFile.json` | Not removed by Foundry |
| Autopilot certificate (PFX and its password), zero-touch upload | Protected with Password protection, recoverable without it | Never written to disk | Not applicable |
| Device hardware hash | Never on the media | `C:\Windows\Temp\Foundry\Logs\AutopilotHash` | Not removed by Foundry |
| Join account and its password, zero-touch Domain Join | Protected. Password protection is required | `C:\Windows\Temp\Foundry\Payloads\DomainJoin\<operation-id>\credentials.bin`, in clear text | By Foundry, as soon as the join has used the file |
| Join account and its password, interactive Domain Join | Not on the media: the technician types them in Foundry Deploy | Same file as zero-touch | Same as zero-touch |

Details the table leaves out:

- Network credentials stay recoverable because Foundry Connect needs them before any password is asked.
- A network certificate private key and its password are copied to the device, in clear text, only when the **Private-key material** row of the deployment summary shows **Included**.
- The hardware hash is in `AutopilotHWID.csv` and `OA3.xml`.
- Foundry restricts the `AutopilotHash` and `DomainJoin` folders to SYSTEM and Administrators.
- Foundry Deploy uses the Autopilot certificate in memory only.
- Interactive hardware hash upload places no secret on the media or on the device: the technician signs in with their own account when the upload runs.

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

## Saved on the workstation

Foundry OSD saves the configuration, its passwords and the files you select for your Windows account, under `%LocalAppData%\Foundry`. The list of what is saved is in [Saved passwords and files](../foundry-osd/configuration-sharing/saved-passwords.md).

| Item | Value |
| --- | --- |
| Options | `Configuration\foundry.config.json`, without any password |
| Passwords | `Configuration\secrets.dat`: the Deployment password, the passwords of local accounts and join accounts, the Wi-Fi passphrase and the PFX passwords |
| Protection of the passwords | Windows DPAPI, for your Windows account. Another Windows account, or a copy of the file on another PC, cannot read them |
| Selected files | `Files\content`: answer files, network profiles, network certificates and the Windows Autopilot PFX, stored as they are, not encrypted by Foundry |
| Publication password and keys of configuration sharing | In `secrets.dat`, with the other passwords |

These passwords are never written to `foundry.config.json`.

## Configuration sharing

An exported file and a publication folder are protected by a secret you choose, not by your Windows account. How to use them is described in [Configuration sharing](../foundry-osd/configuration-sharing.md).

| Item | Exported file | Publication folder |
| --- | --- | --- |
| Secret | The passphrase | The publication password |
| Rule for the secret | At least 12 characters, not the same character repeated | Same rule |
| Passwords of the configuration | Only with **Include passwords** | Only with **Include passwords** |
| Encryption | AES-256-GCM, with a key derived from the passphrase by PBKDF2 with HMAC-SHA-256, 600,000 iterations | Same for each version. Content files are encrypted with AES-256-GCM under a content key carried in the version |
| Signature | None | Every version is signed with an ECDSA P-256 key of the reference PC |

What each person can do with a publication folder:

| Who | Can | Cannot |
| --- | --- | --- |
| Someone who can read the folder, without the password | See the file sizes and `current.json`, which points to the current version | Read the configuration, the passwords or the content |
| Someone who can write in the folder, without the password | Delete files, so followers stop receiving updates | Publish a version, change one, or bring back an older one: followers refuse it |
| Someone who knows the publication password | Read the configuration, the included passwords and the content | Publish: only the key of the reference PC signs a version |
| Another PC that takes the folder over | Publish with its own key, once it has the current password | Be applied by a follower before that follower accepts the new key |

The publication password gives read access; the signature of the reference PC gives the authority to publish. To withdraw read access, the reference PC changes the password or resets the publication:

- **Change password** rewrites the current version under the new password. A former password holder can no longer read new versions, but one who kept the content key can still decrypt the content files.
- **Reset publication** creates a new content key and encrypts every content file again. It is the complete revocation.

On a follower, every setting of the publication is read-only. Only the ISO output path, the Additional WinPE driver folder, the telemetry settings and the passwords that the publication does not include can be changed.
## Windows Autopilot credentials

Zero-touch hardware hash upload uses two separate identities. The rights of the first are never available to the media.

| Identity | Used by | Rights |
| --- | --- | --- |
| The administrator, signed in interactively | Foundry OSD, when you connect the tenant | Delegated Microsoft Graph permissions `Application.ReadWrite.All`, `AppRoleAssignment.ReadWrite.All`, `DeviceManagementServiceConfig.Read.All` and `User.Read`, limited by the administrator's own roles and your Conditional Access policies |
| The app registration `Foundry OSD Autopilot Registration` | Foundry Deploy, on the target device | One Microsoft Graph application permission, `DeviceManagementServiceConfig.ReadWrite.All`, used to import the device and read the result |

Facts that follow from this:

- The app registration is single-tenant. When Foundry creates it, it grants only the permission above. When Foundry adopts an existing registration with the same name, it adds this permission if it is missing and never removes the permissions already there, so check them.
- The media carries the tenant ID, the client ID of the app registration and the certificate PFX with its password. It carries no administrator credential and no sign-in token.
- Foundry Deploy signs in as the app registration with the certificate private key. No technician signs in. The certificate is decrypted in memory and is not copied to the deployed device.
- Anyone who obtains the PFX and its password can use the permission of the app registration until the certificate expires or is removed from the registration.
- On the workstation, Foundry OSD keeps a copy of the PFX and its password for your Windows account: see [Saved on the workstation](#saved-on-the-workstation).

How to create the certificate, choose its validity (12 months at most), select it again, renew it and remove it is described in [Zero-touch hardware hash upload](../foundry-osd/autopilot/zero-touch-hardware-hash.md).

## Join account

During a deployment with Domain Join, Foundry Deploy writes the join account and its password for the domain being joined to `credentials.bin` on the target disk, as listed in the table above. Foundry deletes the file as soon as the join has used it. If the deletion fails or the join was interrupted, the Foundry console after the restart keeps showing `Cleanup: Pending`: see [Cleanup stays Pending](../troubleshooting/domain-join.md#cleanup-pending).

Diagnostic exports never include a file named `credentials.bin`. A copy saved under another name is not recognized, so do not copy or rename that file. How the join accounts are kept in Foundry OSD is described in [Saved on the workstation](#saved-on-the-workstation).

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
