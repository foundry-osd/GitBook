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
| Network cache account passwords | Protected. Password protection is required when a share has an account | Never written to disk: Foundry Deploy uses them only to open the share in Windows PE | Not applicable |
| Join account and its password, interactive Domain Join | Not on the media: the technician types them in Foundry Deploy | Same file as zero-touch | Same as zero-touch |

Details the table leaves out:

- Network credentials stay recoverable because Foundry Connect needs them before any password is asked.
- A network certificate private key and its password are copied to the device, in clear text, only when the **Private-key material** row of the deployment summary shows **Included**.
- The hardware hash is in `AutopilotHWID.csv` and `OA3.xml`.
- Foundry restricts the `AutopilotHash` and `DomainJoin` folders to SYSTEM and Administrators.
- Foundry Deploy uses the Autopilot certificate in memory only.
- Interactive hardware hash upload places no secret on the media or on the device: the technician signs in with their own account when the upload runs.
- The network cache share paths and user names are on the media in clear text. Anyone who holds the media and knows the Deployment password can use a network cache account, so give it rights on the cache shares only: see [Network cache](../foundry-osd/network-cache.md#before-you-start).

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

Foundry OSD saves the configuration, its passwords and the files you select for your Windows account on this PC. The list is in [Saved passwords and files](../foundry-osd/configuration-sharing/saved-passwords.md).

| Item | How it is protected |
| --- | --- |
| Options | Saved without any password |
| Passwords: the Deployment password, the passwords of local accounts, join accounts and network cache accounts, the Wi-Fi passphrase, the PFX passwords, and the publication password of configuration sharing | Encrypted. Only your Windows account on this PC can read them; another account, or a copy of your files on another PC, cannot |
| Selected files: answer files, network profiles, network certificates and the Windows Autopilot PFX | Kept as they are under `%LocalAppData%\Foundry`, not encrypted by Foundry OSD. Anyone who can read your Windows profile can read them |

## Configuration sharing

An exported file and a publication folder are protected by a secret you choose, not by your Windows account. How to use them is described in [Configuration sharing](../foundry-osd/configuration-sharing.md).

- **The secret.** An exported file is protected by its passphrase, a publication folder by its publication password. Both follow the rule the dialogs show: "At least 12 characters, and not the same character repeated (for example aaaaaaaaaaaa)." Keep them as safe as the passwords they protect, and send them by another channel than the file or the folder path.
- **Included passwords.** The passwords of the configuration travel only when **Include passwords** is selected. Then anyone who has the file and its passphrase, or who can open the folder and knows the publication password, can read them.
- **Who can publish.** Knowing the publication password lets someone read the folder, not publish to it. Only the reference PC publishes. A follower accepts a version from another PC only after you [accept the new reference](../foundry-osd/configuration-sharing/follow-a-folder.md#accept-a-new-reference).
- **Write access to the folder.** Someone who can change files in the folder without the password cannot make followers apply a changed or older configuration. They can still delete files, so give write access only to the reference PC.

### Revoke read access

When someone who knew the publication password should no longer read the folder, the reference PC sets a new password:

- **Change password** protects the next versions with the new password, but it does not encrypt the shared content again. Someone who knew the old password and can still open the folder can still read all the shared content (files, post-installation packages, driver sets and images), and the included passwords of the versions published before the change.
- **Reset publication** sets a new password and encrypts all the shared content again. Once the reset is published, people who knew the old password can no longer read anything in the folder.

Use **Reset publication** when someone who leaves must lose access. In both cases, change the passwords that were included in the publication if they must stay secret.

On a follower, every setting of the publication is read-only. Only the ISO output path, the Additional WinPE driver folder, the telemetry settings and the passwords that the publication does not include can be changed.

## Network cache publication <a href="#network-cache-publication" id="network-cache-publication"></a>

With the [network cache](../foundry-osd/network-cache.md) on, Foundry Deploy takes the custom Windows images, custom driver sets and post-installation content from the shares, and runs them on the device. Foundry OSD signs what it publishes, so that a device runs only what an administrator of Foundry OSD published.

- **Who signs.** The reference PC of [configuration sharing](../foundry-osd/configuration-sharing.md), or the single PC when configuration sharing is not used. Each of them signs the index of its publication with a signing key saved, encrypted, for its Windows account on that PC. A follower never signs and never publishes an index.
- **Where the signature is written.** In the index `Foundry\Index\<key id>.json` of each share, next to the content.
- **What the media trust.** The public key of the signing PC: the key of the reference PC or the standalone PC, or, on a follower, the reference key it already accepted. The media carry only this public key. The private key never leaves the signing PC and is never written to the media or to a share.
- **What a device refuses.** Foundry Deploy stops at start, and runs nothing, when the index is missing, was not signed by the trusted key, is damaged, is older than the index known when the media was created, or points to a file that is missing or changed. Someone who can write to a share cannot make devices run content that Foundry OSD did not publish, or go back to an older publication; they can only delete files, in which case devices stop at start with [Network cache not available](../troubleshooting/deployment/before-deployment-starts.md#network-cache-not-available).
- **Reading.** Anyone who can read a share can read the published content, including post-installation scripts. Give read access to the devices and to the administrators only, and keep secrets out of scripts.
- **When the reference PC changes.** The new reference PC signs with its own key and writes its own index beside the previous one. Media created before the change keep trusting the previous reference PC and read its last publication, until you create them again; media created after the change trust the new reference PC. Two PCs running Foundry OSD without configuration sharing coexist the same way, the media of each one reading the publication of the PC that created them. The same applies when a PC loses its saved data, for example after a reset of its Windows password: it signs with a new key, and media created before keep reading its last publication with the previous key. Create the media again.

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
