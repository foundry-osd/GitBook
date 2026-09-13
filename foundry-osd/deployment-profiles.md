# Deployment profiles

Use **Settings > Settings backup and sync** to keep named deployment configurations, transfer them in encrypted packages, and synchronize a profile with a trusted deployment team.

The card header contains the active configuration selector and **More options**. Selecting a configuration saves pending changes to the current profile and applies the selected configuration immediately. The selector always reflects the active configuration; when none is active, it displays **Choose a configuration**. **More options** contains **Rename**, **Duplicate**, and **Delete from this PC**. For a shared configuration, it also provides **Save connection file** when shared access is available.

Expand the card to find separate **Import**, **Export**, **Synchronize**, and **Remember passwords** rows. Each row explains its purpose and places its action beside that explanation. **Synchronize** remains visible for local profiles so you can set up sharing. Shared profiles also show **Automatic sync** and a **Disconnect** action.

A profile contains authoring settings and can retain supported passwords and selected deployment files according to its save and sharing choices. Activating a profile replaces the current authoring configuration. It does not change media that has already been created.

## Create and select a local profile

1. Configure the deployment in Foundry OSD.
2. Open **Settings > Settings backup and sync** and select **More options > Duplicate**.
3. Enter a **Configuration name** and continue. The new independent profile becomes active.
4. To return to another profile, choose it in the profile list. Foundry applies it immediately.

If pending changes cannot be saved or the selected profile cannot be read, Foundry reports the problem and keeps the current profile active. Complete missing inputs or resolve the access problem, then select the profile again.

Changes to the active profile are saved locally after a short pause. New local profiles start with **Remember passwords** enabled. Existing profiles keep their saved choice. **Duplicate** creates an independent local configuration without shared enrollment and inherits the source profile’s **Remember passwords** choice. When enabled, the copy retains available passwords and selected sensitive files. **More options** also contains **Rename** and **Delete from this PC**.

Use the **Remember passwords** switch to change local password retention and confirm the choice. Canceling the dialog or a failed save leaves the switch showing the saved state. Turning the switch off saves a settings-only local revision; it does not clear passwords from the current session or remove the separately remembered shared access key. To clear those too, use the **Clear saved passwords and access** button beside the switch.

Closing Foundry saves pending edits. If the profile cannot be saved, cancel closing to complete the inputs or resolve the storage problem. Choosing **Close** keeps the last successfully saved revision.

A new local profile or a duplicate can initially save the passwords and selected files that are available, even if other required inputs are incomplete. After that initial save, when **Remember passwords** is enabled, subsequent saves wait until required passwords and selected source files are complete. Foundry keeps the last successfully saved revision until then. An incomplete password confirmation or missing required file leaves the current draft on screen and displays a completion message; it does not replace that draft with an older password. Complete the missing inputs to save a new revision. Publishing a shared profile with secrets included requires the same completeness check.

Review [media readiness](media/README.md#review-readiness) after activation. A profile can refer to files, drivers, or credentials that need attention on this workstation.

## Choose what to remember and share

These choices are independent:

| Choice | Effect |
| --- | --- |
| Remember passwords | Retains supported authoring passwords and selected sensitive file contents in the encrypted local profile. |
| Include passwords and confidential files | Includes the available passwords and selected file contents when exporting or creating a shared profile, for recipients who can decrypt it. |
| Remember this connection on this PC | Stores the shared profile access key for this Windows user so synchronization can resume after restarting Foundry OSD. |

Local profile files are encrypted. Windows Credential Manager holds the keys for the Windows user running Foundry OSD, on that computer. Even a settings-only local profile needs its local encryption key. Copying the local profile directory to another computer or running Foundry OSD as another Windows account does not transfer those keys. Use **Export** to transfer an independent copy or the shared configuration’s `Connection.foundryprofile` file to connect another computer. **More options > Save connection file** creates an additional protected copy when shared access is available.

During the first migration from legacy application settings, the new local profile starts with **Remember passwords** enabled and retains applicable saved network passwords and available selected files. An existing profile’s saved choice is preserved, including when remembering is disabled.

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

1. Choose **Import** and select a `.foundryprofile` file. The file picker initially shows `.foundryprofile` files; **All files** remains available as an alternative. Foundry validates the selected file before importing it.
2. Enter its **File password**. Foundry decrypts and validates it before offering activation.
3. Review the profile name and counts of included files, passwords, and access keys. The preview also warns when files are missing or omitted; check their paths before building media.
4. Review **Remember passwords**, which is selected by default when the package is complete and left unchecked when any passwords or files are omitted or unavailable. Clear it if you do not want this PC to retain passwords and sensitive files, then confirm replacement of the current settings.

Remembering requires a complete profile. For a package with omitted or unavailable passwords or files, leave **Remember passwords** unchecked, import the settings, supply the missing inputs, then enable it.

An import creates an independent local copy with a new profile identity. It does not join synchronization, including when the selected file is a connection file. To connect to a shared configuration, choose **Set up synchronization…** in the **Synchronize** row, then **Connect to a shared configuration**. To connect an already shared local configuration to a different share, use **Disconnect** first, then set up synchronization again.

A wrong file password, altered package, or unsupported package version fails validation before activation. Keep the original file and resolve the cause instead of replacing an existing profile with an empty configuration.

## Create a shared profile

Use one authoritative SMB folder for each shared profile. Select a parent UNC folder such as `\\server\share`, with Windows-authenticated access restricted to the intended team. It can already contain files. Foundry creates a separate folder at `\\server\share\Foundry\<configuration name>`. Require SMB 3 encryption according to your server policy. Foundry uses the access available to the Windows user; it does not manage share permissions or enable SMB encryption.

Do not place the shared repository in a OneDrive, Dropbox, or other cloud-synchronized mirror. Multiple independent replicas do not provide the locking and conditional publication expected by this workflow. Use a NAS only after validating its SMB locking, rename, reconnect, and failover behavior with your deployment environment; a working file browser alone does not establish compatibility.

1. Activate the local profile, choose **Set up synchronization…** in the **Synchronize** row, then **Share this configuration**.
2. Enter a **Configuration name**; the current name is filled in for you. Use a valid Windows folder name without a trailing dot or space.
3. Enter the shared parent folder or choose **Browse** beside the path field. Check the full destination path shown below the field; it updates as you change the name or parent folder.
4. Enter a **Connection password** and repeat it in **Confirm password**. Other PCs will need this password to connect. A mismatch appears beside the confirmation field as you enter it and is checked again when you select **Share**.
5. Choose whether to **Include passwords and confidential files** in shared updates. Enabling this choice displays a reminder that people using the configuration can read those passwords and files.
6. Choose whether to **Remember this connection on this PC**, then select **Share**.
7. Foundry creates `Connection.foundryprofile` inside the new shared configuration folder. Check synchronization status and give the other PCs access to this file, with its password supplied separately.

The dialog keeps the configuration name, folder, password, and password confirmation together, followed by the two sharing choices. Its width adapts to smaller windows, with related choices and helper text grouped together. The main action is highlighted; destructive confirmations keep **Cancel** as the default. Select **Learn more** for the shared-folder requirements and recovery guidance on this page.

If the named folder already exists, choose **Connect** and provide its connection file, or **Choose another name**. Existing folders and files are not overwritten. Renaming the configuration later changes its display name while keeping its synchronization folder in place.

Every member with the shared key is trusted to read included secrets and publish revisions. Share permissions and package encryption do not create a hidden-password role for team members who hold that key.

## Export recovery material and join the team

Sharing creates a password-protected `Connection.foundryprofile` file in the configuration’s shared folder. This encrypted connection file also serves as recovery material: it includes the shared identity and access key needed to enroll another workstation, plus profile content according to the sharing choice. It carries that secret-inclusion choice and, when present, the shared folder path to the joined workstation.

For an existing shared configuration that needs a connection file, or to create another protected copy, choose **More options > Save connection file** and enter a file password. This action is available when this PC has shared access.

Keep a protected recovery copy **outside the shared repository** as well. The automatically created connection file stays inside the shared folder for convenient team enrollment; manual **Save connection file** exports must use a separate destination. Designate a primary and a backup custodian and arrange controlled access to the file password. The encrypted shared revisions alone cannot replace a lost access key.

On another workstation:

1. Choose **Set up synchronization…** in the **Synchronize** row, then **Connect to a shared configuration**, and select `Connection.foundryprofile` from the team’s shared configuration folder or a protected copy supplied by your team. The file picker initially shows `.foundryprofile` files, with **All files** available as an alternative. If this PC is already enrolled but has lost its access key, choose **Restore access** instead.
2. Enter the **Connection password** and review the preview.
3. Check the configuration’s full UNC folder, such as `\\server\share\Foundry\Deployment - Paris`, rather than its parent share. Foundry fills in the path carried by the connection file or the existing-name prompt. Older files without a path require you to enter it.
4. Review **Remember passwords**, which is selected by default when the connection file contains a complete profile. It is left unchecked when any passwords or files are omitted or unavailable; supply the missing inputs after connecting, then enable it. Choose **Remember this connection on this PC** separately; it controls access to synchronization, not local password retention.
5. Continue to validate the shared identity and activate its current revision.

If a connection file contains a NAS name that this PC cannot resolve, open the file directly through a reachable server name or IP address. When the selected file is in the same share and relative configuration folder as the saved path, Foundry prefills that reachable server address. A file opened from a local backup or a different folder keeps its saved path; check and correct the shared folder before connecting. This changes the suggested address only: Foundry still validates the shared configuration’s identity and access key.

If you do not remember the shared key, it remains available for the current session. After a restart or a switch that clears it, use **Restore access** with the connection file and its password. Keep both accessible before relying on this policy.

## Synchronize and resolve conflicts

The **Synchronize** row shows **Set up synchronization…** for a local profile. A shared profile provides **Synchronize** and **Disconnect**. When this PC lacks its shared access key, **Restore access** replaces the synchronization action. Enable the separate **Automatic sync** switch to check for changes at startup and every **30 seconds** afterward, or choose **Synchronize** when needed. Local edits are saved after about **750 milliseconds** of inactivity; the next automatic check publishes them. Checks defer while a dialog or operation is running and for two seconds after an edit. Turning off automatic synchronization keeps the local profile available for editing and manual synchronization.

Choose **Disconnect** and confirm to stop synchronizing this configuration on this PC. Foundry keeps the current local settings and password-remembering choice, removes this PC’s shared connection and retained shared access, and leaves the shared configuration and other PCs unchanged. To connect to a different shared configuration afterward, choose **Set up synchronization…**.

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

Conflict decisions apply to the whole profile, not individual fields. If the shared revision changes again before your decision completes, resolve the new conflict. Use **Duplicate** first when you need to preserve your draft. The copy inherits the source profile’s password-remembering choice and, when enabled, retains available passwords and selected sensitive files. Review that choice before resolving the conflict.

Foundry distinguishes an unreachable folder from a folder temporarily in use. For an unreachable folder, check its address and this PC’s network access. For a busy folder, retry shortly. In both cases, edits remain local.

While the share is unavailable, edits remain local. If a connection fails during publication, Foundry keeps the encrypted pending operation and checks whether that exact operation committed before retrying it. Keep local profile and pending-operation files intact while recovery is unresolved.

Recovery recognizes changes that were already shared and avoids publishing them twice. Edits made afterward remain pending, and simultaneous changes from another PC still require a conflict decision. Turning automatic synchronization off or on preserves changes waiting to be shared.

At startup, Foundry checks the active shared configuration in the background after local settings and startup readiness are restored. With automatic synchronization enabled and the shared access key available, it can apply a remote update before you start editing, including from Home. Conflicting local changes still require your choice. If you did not remember the connection on this PC, use **Restore access** after restarting.

After that startup check, automatic activation of a remote update waits until the **Settings backup and sync** card in Settings is open and no profile dialog or media operation is running. During editing elsewhere, an available update does not silently replace the authoring configuration. Return to the card, review status, and use **Synchronize** when ready.

### Deletion, history, and key replacement

**More options > Delete from this PC** removes that workstation's enrollment. Deleting the shared profile requires **Also delete for everyone** and a separate confirmation. A remote deletion is reported to other editors; preserve needed local work with **Duplicate** rather than attempting to revive the deleted profile.

A shared configuration can keep receiving updates without a lifetime publication limit. After safely saving an update, Foundry keeps the **20 most recent encrypted configuration snapshots** and removes older snapshot files. A cleanup failure produces a warning in the logs and does not prevent the update from being published.

Small synchronization records remain available so PCs returning after time offline can reconnect and interrupted updates can recover safely. These records continue to accumulate, so the 20-snapshot retention rule does not cap the total file count or storage used by the shared folder. Let Foundry manage snapshot cleanup; do not manually delete synchronization records or replace them with an older backup.

Update every PC using the shared configuration to use this retention behavior. Existing shared folders and connection files remain usable without disconnecting or creating a new shared configuration.

To replace a shared access key, **Disconnect** the reviewed configuration, then choose **Set up synchronization… > Share this configuration** with a new configuration name or parent folder. Foundry creates a new connection file for enrolling the team against that folder and leaves the old repository intact. If you need to keep the original local enrollment too, **Duplicate** the reviewed configuration instead and review the copy’s password and sharing choices before setting up the new folder. Old key holders can continue using that repository while they have access to it: remove their old share permissions or archive the folder with restricted access. A new key cannot revoke secrets or packages that someone already copied. Rotate the underlying passwords and certificates when exposure requires it.

## Create media from the selected profile

Select the intended profile, check passwords and source files, and resolve readiness issues before starting media creation. Foundry captures the configuration, secret values, and selected file bytes for that build. A later edit or shared update does not change the inputs of the running build.

Custom drivers remain local and are copied into the build snapshot. The selected driver source must fit within **2 GiB** and **10,000 filesystem entries**; use a focused driver source for the target hardware.

Foundry removes its temporary copies after media creation. If a locked file or an interrupted session prevents cleanup, it retries when Foundry starts or prepares another build. Temporary files belonging to an active build remain available until that build finishes.

Profile package encryption does not replace [Protected deployment](general.md#protected-deployment) or the [security requirements for deployment media](../reference/security-and-credentials.md). Recreate media when its embedded credentials or deployment settings change.
