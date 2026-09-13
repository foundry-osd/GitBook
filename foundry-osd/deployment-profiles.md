# Deployment profiles

Use **Settings > Settings backup and sync** to keep named deployment configurations, transfer them in encrypted packages, and synchronize a profile with a trusted deployment team.

The card header contains the active configuration selector and **More options**. Selecting a configuration saves pending changes to the current profile and applies the selected configuration immediately. The selector always reflects the active configuration; when none is active, it displays **Choose a configuration**. **More options** contains **Rename**, **Duplicate**, and **Delete from this PC**.

Expand the card to find separate **Import**, **Export**, **Synchronize**, and **Remember passwords** rows. Each row explains its purpose and places its action beside that explanation. **Synchronize** remains visible for local profiles so you can set up sharing; shared profiles also show **Automatic sync** and **Save connection file**.

A profile contains authoring settings and, when explicitly included, passwords and selected deployment files. Activating a profile replaces the current authoring configuration. It does not change media that has already been created.

## Create and select a local profile

1. Configure the deployment in Foundry OSD.
2. Open **Settings > Settings backup and sync** and select **More options > Duplicate**.
3. Enter a **Configuration name** and continue. The new independent profile becomes active.
4. To return to another profile, choose it in the profile list. Foundry applies it immediately.

If pending changes cannot be saved or the selected profile cannot be read, Foundry reports the problem and keeps the current profile active. Complete missing inputs or resolve the access problem, then select the profile again.

Changes to the active profile are saved locally after a short pause. **Duplicate** creates an independent local configuration without shared enrollment; it starts with password remembering disabled. **More options** also contains **Rename** and **Delete from this PC**.

Use the **Remember passwords** switch to change local password retention and confirm the choice. Canceling the dialog or a failed save leaves the switch showing the saved state. Turning the switch off saves a settings-only local revision; it does not clear passwords from the current session or remove the separately remembered shared access key. To clear those too, use **Clear saved passwords and access** in the same row.

Closing Foundry saves pending edits. If the profile cannot be saved, cancel closing to complete the inputs or resolve the storage problem. Choosing **Close** keeps the last successfully saved revision.

When **Remember passwords** is enabled, Foundry keeps the last complete saved revision until required passwords and selected source files are available. An incomplete password confirmation or missing required file leaves the current draft on screen and displays a completion message; it does not replace that draft with an older password. Complete the missing inputs to save a new revision. Publishing a shared profile with secrets included requires the same completeness check.

Review [media readiness](media/README.md#review-readiness) after activation. A profile can refer to files, drivers, or credentials that need attention on this workstation.

## Choose what to remember and share

These choices are independent:

| Choice | Effect |
| --- | --- |
| Remember passwords | Retains supported authoring passwords and selected sensitive file contents in the encrypted local profile. |
| Include passwords and confidential files | Includes the available passwords and selected file contents when exporting or creating a shared profile, for recipients who can decrypt it. |
| Remember this connection on this PC | Stores the shared profile access key for this Windows user so synchronization can resume after restarting Foundry OSD. |

Local profile files are encrypted. Windows Credential Manager holds the keys for the Windows user running Foundry OSD, on that computer. Even a settings-only local profile needs its local encryption key. Copying the local profile directory to another computer or running Foundry OSD as another Windows account does not transfer those keys. Use **Export** to transfer an independent copy or **Save connection file** to connect another computer to a shared configuration.

During the first migration from legacy application settings, applicable saved network passwords remain available in the current session. Choose **Remember passwords** explicitly to retain them in the new local profile; migration does not enable that choice automatically.

Supported password contexts include Wi-Fi, Protected deployment, OOBE local accounts, and selected network or Autopilot PFX certificates. Required passwords must be present and meet their validation rules, including matching confirmation where requested. Configure an intentionally blank OOBE password through its explicit password choice; an empty required password field is incomplete. Profile activation does not fill missing values from an unrelated profile.

Microsoft sign-in sessions and token caches are not transferred. An Autopilot tenant connection still requires the appropriate sign-in and permissions. [Desktop proxy credentials](settings.md#proxy) remain separate from deployment profiles and do not configure Windows PE.

An imported Autopilot configuration with a valid saved registration can use its matching PFX certificate without a new Microsoft sign-in. If the file was omitted, select that certificate and enter its password on this PC. A different or expired certificate does not satisfy readiness checks. Creating or retiring certificates still requires a tenant connection.

### Forget retained secrets

Choose **Clear saved passwords and access** in the **Remember passwords** row and confirm. Foundry saves a settings-only local revision, clears the active profile's session secrets and shared key, disables synchronization for that enrollment, and retires its earlier stored keys. Re-enter required passwords before building media.

If cleanup cannot complete, follow the reported status and retry when Windows Credential Manager and local storage are available. Do not assume all retained material has been removed while cleanup remains pending.

Clearing saved passwords and access does not remove exported packages, previously published shared revisions, original source files, or existing deployment media. Disabling password remembering also does not disable the separate remembered shared key. Use **Clear saved passwords and access** when you want to stop that enrollment from restoring shared data automatically.

## Export an encrypted package

1. Activate the profile to transfer.
2. Choose **Export** and select a destination for the `.foundryprofile` file.
3. Enter a **File password** and repeat it in **Confirm file password**.
4. Choose whether to **Include passwords and confidential files**, then continue.

With secret inclusion disabled, the package transfers settings without password values or attached sensitive file contents. Inline Autopilot JSON profile configuration remains part of the settings; review it before distribution.

When synchronizing without confidential files, changes made on a PC that lacks a certificate do not remove the matching certificate and password already available on another PC. Changing or removing the selected source clears that association. Each PC still needs its own required files and passwords before it can create media.

With secret inclusion enabled, Foundry captures available files already selected in the configuration: custom answer files, wired or enterprise Wi-Fi XML profiles, and network or Autopilot PFX certificates. This choice applies to the selected dependencies; it is not a browser for arbitrary folders. Missing files are not recovered from another workstation.

Packages accept at most **64 assets**, **4 MiB per asset**, and **8 MiB of asset contents in total**, within a **16 MiB profile payload**. Large Windows images, driver collections, and media outputs are not profile attachments. Keep their distribution separate.

Portable packages exclude workstation-specific paths and physical disk targets. After import, verify local sources and output destinations, reselect any omitted files, and remap custom driver directories. Included files are validated and staged beneath Foundry's local managed storage.

Send the file password through a separate trusted channel. Anyone who can decrypt a package can access the secrets it includes. Treat custom answer files and private-key certificates as sensitive even when their filenames look ordinary.

## Preview and import a copy

1. Choose **Import** and select a `.foundryprofile` file.
2. Enter its **File password**. Foundry decrypts and validates it before offering activation.
3. Review the profile name and counts of included files, passwords, and access keys. The preview also warns when files are missing or omitted; check their paths before building media.
4. Choose whether to **Remember passwords** on this PC, then confirm replacement of the current settings.

Remembering requires a complete profile. For a package with omitted or unavailable passwords or files, import without remembering, supply the missing inputs, then enable **Remember passwords**.

An import creates an independent local copy with a new profile identity. It does not join synchronization, including when the selected file is a connection file. To connect to a shared configuration, choose **Set up synchronization…** in the **Synchronize** row, then **Connect to a shared configuration**. For an already shared profile, choose **Connection settings…** beside **Synchronize**.

A wrong file password, altered package, or unsupported package version fails validation before activation. Keep the original file and resolve the cause instead of replacing an existing profile with an empty configuration.

## Create a shared profile

Use one authoritative SMB folder for each shared profile. Prepare a dedicated UNC path such as `\\server\share\deployment-profile`, with Windows-authenticated access restricted to the intended team. Require SMB 3 encryption according to your server policy. Foundry uses the access available to the Windows user; it does not manage share permissions or enable SMB encryption.

Do not place the shared repository in a OneDrive, Dropbox, or other cloud-synchronized mirror. Multiple independent replicas do not provide the locking and conditional publication expected by this workflow. Use a NAS only after validating its SMB locking, rename, reconnect, and failover behavior with your deployment environment; a working file browser alone does not establish compatibility.

1. Activate the local profile, choose **Set up synchronization…** in the **Synchronize** row, then **Share this configuration**.
2. Enter or browse to the dedicated UNC folder.
3. Choose whether to **Include passwords and confidential files** in shared updates.
4. Choose whether to **Remember this connection on this PC**, then continue.
5. Check synchronization status and choose **Save connection file** to prepare access for other computers and key recovery.

Every member with the shared key is trusted to read included secrets and publish revisions. Share permissions and package encryption do not create a hidden-password role for team members who hold that key.

## Export recovery material and join the team

Choose **Save connection file** for the shared profile and protect the `.foundryprofile` file with a password. This encrypted connection file also serves as recovery material: it includes the shared identity and access key needed to enroll another workstation, plus profile content according to the sharing choice. It also carries that secret-inclusion choice to the joined workstation.

Store recovery material **outside the shared repository**; Foundry rejects exports inside that folder. Designate a primary and a backup custodian, keep protected copies in an approved separate location, and arrange controlled access to the file password. A backup of the shared folder alone cannot replace a lost decryption key.

On another workstation:

1. Choose **Set up synchronization…** in the **Synchronize** row, then **Connect to a shared configuration**, and select the connection file supplied by your team. For an already shared profile, choose **Connection settings…** beside **Synchronize**.
2. Enter the file password and review the preview.
3. Provide the authoritative UNC folder.
4. Choose **Remember passwords** and **Remember this connection on this PC** separately.
5. Continue to validate the shared identity and activate its current revision.

If you do not remember the shared key, it remains available for the current session. After a restart or a switch that clears it, use **Connection settings… > Connect to a shared configuration** with the connection file to join again. Keep the connection file and its password accessible before relying on this policy.

## Synchronize and resolve conflicts

The **Synchronize** row shows **Set up synchronization…** for a local profile. A shared profile has separate **Synchronize** and **Connection settings…** buttons: the first synchronizes changes, while the second changes or restores the shared connection. For a shared profile, enable the separate **Automatic sync** switch to check for changes periodically, or choose **Synchronize** when needed. Turning off automatic synchronization keeps the local profile available for editing and manual synchronization.

A colored icon and a short status label beside the synchronization buttons show the current state:

| Status | Meaning |
| --- | --- |
| Up to date — green | The last synchronization succeeded and this PC has no changes waiting to be shared. |
| Synchronizing… / Changes to synchronize / Changes available — informational color | A check is running, this PC has changes to share, or shared changes are ready to apply. |
| Choose a version / Folder unavailable / Needs attention — amber | Review the conflict or the detailed message below the card. |
| Unable to synchronize — red | Resolve the reported access, format, or storage problem before retrying. |
| Not configured / Ready to synchronize — neutral | Set up synchronization, or run a check to confirm the shared settings are current. |

The icon and text communicate the state together. Colors follow the Windows theme, and longer labels wrap in compact layouts. Detailed errors and conflict actions remain below the card. Automatic checks do not repeatedly announce an unchanged result to a screen reader.

Foundry publishes only against the shared revision it last read. When another editor publishes first, it preserves the local draft and reports a conflict instead of choosing a winner by file timestamp. Coordinate with the other editor before choosing an action:

| Action | Result |
| --- | --- |
| Use the shared version | Replaces the local draft with the current shared profile. |
| Use this PC's version | Publishes the local draft against the reviewed shared revision, provided that revision has not changed again. |
| Duplicate | Preserves the current configuration as an independent local profile without shared enrollment. |

Conflict decisions apply to the whole profile, not individual fields. If the shared revision changes again before your decision completes, resolve the new conflict. Save a copy first when you need to preserve your draft; enable local secret remembering on that copy if required.

While the share is unavailable, edits remain local. If a connection fails during publication, Foundry keeps the encrypted pending operation and checks whether that exact operation committed before retrying it. Keep local profile and pending-operation files intact while recovery is unresolved.

Recovery recognizes changes that were already shared and avoids publishing them twice. Edits made afterward remain pending, and simultaneous changes from another PC still require a conflict decision. Turning automatic synchronization off or on preserves changes waiting to be shared.

Automatic activation of a remote update waits until the **Settings backup and sync** card in Settings is open and no profile dialog or media operation is running. During editing elsewhere, an available update does not silently replace the authoring configuration. Return to the card, review status, and use **Synchronize** when ready.

### Deletion, history, and key replacement

**More options > Delete from this PC** removes that workstation's enrollment. Deleting the shared profile requires **Also delete for everyone** and a separate confirmation. A remote deletion is reported to other editors; preserve needed local work with **Duplicate** rather than attempting to revive the deleted profile.

The shared repository accepts up to **4,096 revisions**. When the history limit is reached, create a fresh shared repository from the reviewed current profile and distribute new recovery material. Do not manually remove revision files or substitute an older backup to bypass a history or rollback warning.

To replace a shared access key, choose **Connection settings… > Share this configuration** with a fresh dedicated folder, then save a new connection file and enroll the team against that folder. This replaces the local enrollment and leaves the old repository intact. If you need to keep the original local enrollment too, **Duplicate** the reviewed configuration first and review the copy's password and sharing choices before setting up the new folder. Old key holders can continue using that repository while they have access to it: remove their old share permissions or archive the folder with restricted access. A new key cannot revoke secrets or packages that someone already copied. Rotate the underlying passwords and certificates when exposure requires it.

## Create media from the selected profile

Select the intended profile, check passwords and source files, and resolve readiness issues before starting media creation. Foundry captures the configuration, secret values, and selected file bytes for that build. A later edit or shared update does not change the inputs of the running build.

Custom drivers remain local and are copied into the build snapshot. The selected driver source must fit within **2 GiB** and **10,000 filesystem entries**; use a focused driver source for the target hardware.

Foundry removes its temporary copies after media creation. If a locked file or an interrupted session prevents cleanup, it retries when Foundry starts or prepares another build. Temporary files belonging to an active build remain available until that build finishes.

Profile package encryption does not replace [Protected deployment](general.md#protected-deployment) or the [security requirements for deployment media](../reference/security-and-credentials.md). Recreate media when its embedded credentials or deployment settings change.
