# Deployment profiles

Use **Settings > Deployment profiles** to keep named deployment configurations, transfer them in encrypted packages, and synchronize a profile with a trusted deployment team.

A profile contains authoring settings and, when explicitly included, passwords and selected deployment files. Activating a profile replaces the current authoring configuration. It does not change media that has already been created.

## Create and select a local profile

1. Configure the deployment in Foundry OSD.
2. Open **Settings > Deployment profiles** and select **Save as copy**.
3. Enter a name and continue. The new independent profile becomes active.
4. To return to another profile, select it in the profile list, choose **Open**, and confirm replacement.

Changes to the active profile are saved locally after a short pause. **Save as copy** creates a separate identity without shared enrollment; it starts with password remembering disabled. Use the profile management menu to rename, change password retention, export, or delete the active profile.

Closing Foundry saves pending edits. If the profile cannot be saved, cancel closing to complete the inputs or resolve the storage problem. Choosing **Close** keeps the last successfully saved revision.

When **Remember passwords** is enabled, Foundry keeps the last complete saved revision until required passwords and selected source files are available. An incomplete password confirmation or missing required file leaves the current draft on screen and displays a completion message; it does not replace that draft with an older password. Complete the missing inputs to save a new revision. Publishing a shared profile with secrets included requires the same completeness check.

Review [media readiness](media/README.md#review-readiness) after activation. A profile can refer to files, drivers, or credentials that need attention on this workstation.

## Choose what to remember and share

These choices are independent:

| Choice | Effect |
| --- | --- |
| Remember passwords | Retains supported authoring passwords and selected sensitive file contents in the encrypted local profile. |
| Include secrets and confidential files | Includes the available passwords and selected file contents when exporting or creating a shared profile, for recipients who can decrypt it. |
| Remember the shared key | Stores the shared profile access key for this Windows user so synchronization can resume after restarting Foundry OSD. |

Local profile files are encrypted. Windows Credential Manager holds the keys for the Windows user running Foundry OSD, on that computer. Even a settings-only local profile needs its local encryption key. Copying the local profile directory to another computer or running Foundry OSD as another Windows account does not transfer those keys. Use an encrypted export or recovery package for transfer.

During the first migration from legacy application settings, applicable saved network passwords remain available in the current session. Choose **Remember passwords** explicitly to retain them in the new local profile; migration does not enable that choice automatically.

Supported password contexts include Wi-Fi, Protected deployment, OOBE local accounts, and selected network or Autopilot PFX certificates. Required passwords must be present and meet their validation rules, including matching confirmation where requested. Configure an intentionally blank OOBE password through its explicit password choice; an empty required password field is incomplete. Profile activation does not fill missing values from an unrelated profile.

Microsoft sign-in sessions and token caches are not transferred. An Autopilot tenant connection still requires the appropriate sign-in and permissions. [Desktop proxy credentials](settings.md#proxy) remain separate from deployment profiles and do not configure Windows PE.

### Forget retained secrets

Choose **Forget credentials** in the profile management menu and confirm. Foundry saves a settings-only local revision, clears the active profile's session secrets and shared key, disables synchronization for that enrollment, and retires its earlier stored keys. Re-enter required passwords before building media.

If cleanup cannot complete, follow the reported status and retry when Windows Credential Manager and local storage are available. Do not assume all retained material has been removed while cleanup remains pending.

Forgetting local secrets does not remove exported packages, previously published shared revisions, original source files, or existing deployment media. Disabling password remembering also does not disable the separate remembered shared key. Use **Forget credentials** when you want to stop that enrollment from restoring shared data automatically.

## Export an encrypted package

1. Activate the profile to transfer.
2. Choose **Export encrypted profile** and select a destination for the `.foundryprofile` file.
3. Enter and confirm a package passphrase.
4. Choose whether to include secrets, then continue.

With secret inclusion disabled, the package transfers settings without password values or attached sensitive file contents. Inline Autopilot JSON profile configuration remains part of the settings; review it before distribution.

With secret inclusion enabled, Foundry captures available files already selected in the configuration: custom answer files, wired or enterprise Wi-Fi XML profiles, and network or Autopilot PFX certificates. This choice applies to the selected dependencies; it is not a browser for arbitrary folders. Missing files are not recovered from another workstation.

Packages accept at most **64 assets**, **4 MiB per asset**, and **8 MiB of asset contents in total**, within a **16 MiB profile payload**. Large Windows images, driver collections, and media outputs are not profile attachments. Keep their distribution separate.

Portable packages exclude workstation-specific paths and physical disk targets. After import, verify local sources and output destinations, reselect any omitted files, and remap custom driver directories. Included files are validated and staged beneath Foundry's local managed storage.

Send the passphrase through a separate trusted channel. Anyone who can decrypt a package can access the secrets it includes. Treat custom answer files and private-key certificates as sensitive even when their filenames look ordinary.

## Preview and import a copy

1. Choose **Import as copy** and select a `.foundryprofile` file.
2. Enter its passphrase. Foundry decrypts and validates it before offering activation.
3. Review the profile name and counts of included files and secrets. The preview also warns when files are missing or omitted; check their paths before building media.
4. Choose whether to remember secrets locally, then confirm replacement of the current authoring configuration.

Remembering requires a complete profile. For a package with omitted or unavailable passwords or files, import without remembering, supply the missing inputs, then enable **Remember passwords**.

An import creates an independent local copy with a new profile identity. It does not join synchronization, including when the selected file is a recovery package. Use **Join from recovery file** for shared enrollment.

A wrong passphrase, altered package, or unsupported package version fails validation before activation. Keep the original file and resolve the cause instead of replacing an existing profile with an empty configuration.

## Create a shared profile

Use one authoritative SMB folder for each shared profile. Prepare a dedicated UNC path such as `\\server\share\deployment-profile`, with Windows-authenticated access restricted to the intended team. Require SMB 3 encryption according to your server policy. Foundry uses the access available to the Windows user; it does not manage share permissions or enable SMB encryption.

Do not place the shared repository in a OneDrive, Dropbox, or other cloud-synchronized mirror. Multiple independent replicas do not provide the locking and conditional publication expected by this workflow. Use a NAS only after validating its SMB locking, rename, reconnect, and failover behavior with your deployment environment; a working file browser alone does not establish compatibility.

1. Activate the local profile and choose **Create shared profile**.
2. Enter or browse to the dedicated UNC folder.
3. Choose whether published revisions include secrets.
4. Choose whether to remember the shared key on this computer, then continue.
5. Check synchronization status and export a recovery package.

Every member with the shared key is trusted to read included secrets and publish revisions. Share permissions and package encryption do not create a hidden-password role for team members who hold that key.

## Export recovery material and join the team

Choose **Export recovery file** for the shared profile and protect the `.foundryprofile` file with a passphrase. This package includes the shared identity and access key needed to enroll another workstation, plus profile content according to the sharing choice. It also carries that secret-inclusion choice to the joined workstation.

Store recovery material **outside the shared repository**; Foundry rejects exports inside that folder. Designate a primary and a backup custodian, keep protected copies in an approved separate location, and arrange controlled access to the passphrase. A backup of the shared folder alone cannot replace a lost decryption key.

On another workstation:

1. Choose **Join from recovery file** and select the recovery package.
2. Enter the passphrase and review the preview.
3. Provide the authoritative UNC folder.
4. Choose local password remembering and shared-key remembering separately.
5. Continue to validate the shared identity and activate its current revision.

If you do not remember the shared key, it remains available for the current session. After a restart or a switch that clears it, use the recovery package to join again. Keep the recovery material accessible before relying on this policy.

## Synchronize and resolve conflicts

Enable **Automatic sync** to check for changes periodically, or use **Sync now**. Turning off automatic synchronization keeps the local profile available; use **Sync now** when you want to synchronize manually.

Foundry publishes only against the shared revision it last read. When another editor publishes first, it preserves the local draft and reports a conflict instead of choosing a winner by file timestamp. Coordinate with the other editor before choosing an action:

| Action | Result |
| --- | --- |
| Use shared version | Replaces the local draft with the current shared profile. |
| Keep local version | Publishes the local draft against the reviewed shared revision, provided that revision has not changed again. |
| Save as copy | Preserves the current configuration as an independent local profile without shared enrollment. |

Conflict decisions apply to the whole profile, not individual fields. If the shared revision changes again before your decision completes, resolve the new conflict. Save a copy first when you need to preserve your draft; enable local secret remembering on that copy if required.

While the share is unavailable, edits remain local. If a connection fails during publication, Foundry keeps the encrypted pending operation and checks whether that exact operation committed before retrying it. Keep local profile and pending-operation files intact while recovery is unresolved.

Automatic activation of a remote update waits until the Deployment profiles card in Settings is open and no profile dialog or media operation is running. During editing elsewhere, an available update does not silently replace the authoring configuration. Return to the card, review status, and use **Sync now** when ready.

### Deletion, history, and key replacement

**Delete local profile** removes that workstation's enrollment. Deleting the shared profile requires **Also delete the shared profile** and a separate confirmation. A remote deletion is reported to other editors; preserve needed local work with **Save as copy** rather than attempting to revive the deleted profile.

The shared repository accepts up to **4,096 revisions**. When the history limit is reached, create a fresh shared repository from the reviewed current profile and distribute new recovery material. Do not manually remove revision files or substitute an older backup to bypass a history or rollback warning.

To replace a shared access key, use **Create shared profile** with a fresh dedicated folder, then export a new recovery package and enroll the team against that folder. This replaces the local enrollment and leaves the old repository intact. Old key holders can continue using that repository while they have access to it: remove their old share permissions or archive the folder with restricted access. A new key cannot revoke secrets or packages that someone already copied. Rotate the underlying passwords and certificates when exposure requires it.

## Create media from the selected profile

Select the intended profile, check passwords and source files, and resolve readiness issues before starting media creation. Foundry captures the configuration, secret values, and selected file bytes for that build. A later edit or shared update does not change the inputs of the running build.

Custom drivers remain local and are copied into the build snapshot. The selected driver source must fit within **2 GiB** and **10,000 filesystem entries**; use a focused driver source for the target hardware.

Profile package encryption does not replace [Protected deployment](general.md#protected-deployment) or the [security requirements for deployment media](../reference/security-and-credentials.md). Recreate media when its embedded credentials or deployment settings change.
