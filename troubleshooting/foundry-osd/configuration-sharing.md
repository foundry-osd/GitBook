# Configuration sharing troubleshooting

Use this page for a status or a message of the **Configuration sharing** card of **Settings** in Foundry OSD, or of its dialogs. The rest of the application is in [Foundry OSD application troubleshooting](../foundry-osd.md).

The status is the colored bar under the role of the PC, "This PC publishes its configuration" or "This PC follows \<PC name\>". Your changes are always saved on the PC, whatever the status.

**Collect** names the Foundry OSD log, `%ProgramData%\Foundry\Logs\Foundry.log`: see [Log locations](../logs-and-support.md#log-locations). Never send an exported file, the content of a publication folder, a passphrase or a publication password.

## Statuses that need nothing

| Status | Meaning |
| --- | --- |
| "This PC neither publishes nor follows a configuration." | The PC uses its own configuration. |
| "Changes are published automatically.", "Publishing the configuration…", "Published. Followers receive this version at their next check." | The reference PC works normally. |
| "Waiting for the first check of the folder.", "Up to date.", "Configuration updated." | The follower works normally. |
| "The configuration is applied. Shared content is being downloaded." | The follower downloads and installs new packages, driver sets or images. Media creation is blocked until they are installed, then becomes available by itself. If the download fails or stalls, the status changes: see [Shared content missing](#content-failed). |

## Symptom index

| What you see | Role | Go to |
| --- | --- | --- |
| "Not published: the configuration is not ready to create media ..." | Reference | [Not published](#not-published) |
| "The folder is unavailable. ..." | Both | [Folder unavailable](#folder-unavailable) |
| "This folder holds files that are not a publication of this PC. ..." or "This folder does not hold a published configuration." | Both | [Not a publication](#not-a-publication) |
| "Another PC publishes to this folder. ..." | Reference | [Another PC publishes](#another-pc-publishes) |
| "The folder is now published by another PC. ..." | Follower | [New reference](#new-reference) |
| "The publication password does not open ..." or "This password does not open the publication, or the publication was modified." | Both | [Wrong password](#wrong-password) |
| "Passwords are needed: ..." or "The publication password or keys of this PC could not be read." | Both | [Passwords needed](#passwords-needed) |
| "The publication was modified. It is not applied." | Follower | [Publication modified](#tampered) |
| "The folder points to an older version than the one applied. It is ignored." | Follower | [Older version](#rollback-rejected) |
| "This publication requires a newer version of Foundry OSD." or "This file requires a newer version of Foundry OSD." | Follower, import | [Newer version required](#unsupported) |
| "Checking the folder: the publication has not fully arrived yet ..." | Follower | [Publication not arrived](#checking) |
| "The configuration is applied, but some shared content could not be installed." or "The configuration is applied, but some shared content is still not available after repeated checks." | Follower | [Shared content missing](#content-failed) |
| "Publishing and following are unavailable during this session ..." | Both | [Sharing unavailable](#sharing-unavailable) |
| A message of the **Export configuration** or **Import configuration** dialog | Any | [Export and import messages](#export-and-import-messages) |

## "Not published: the configuration is not ready to create media, or it references content missing on this PC. The previous version stays current." <a href="#not-published" id="not-published"></a>

**Where:** Status of the reference PC.

**Cause:** Foundry OSD publishes only a configuration that can create media. A row of **Start** needs attention, or a post-installation package, driver set or custom image used by the configuration is missing on this PC.

**Fix:** Open [Start](../../foundry-osd/media/README.md) and clear every row marked **Needs attention**. The next change publishes; **Publish now** publishes at once. Followers keep the previous version meanwhile.

**Collect:** Nothing.

## "The folder is unavailable. Check that it exists and that this PC can access it." <a href="#folder-unavailable" id="folder-unavailable"></a>

**Where:** Status of either role, or the **Publish to a folder** and **Follow a folder** dialogs.

**Cause:** The path is wrong, the network, VPN or file server is not available, your Windows account cannot read the folder (or write in it, on the reference PC), or the synchronization client of a OneDrive or SharePoint folder is not running.

**Fix:**

1. Open the folder in File Explorer with the same Windows account. On the reference PC, check that you can create a file in it.
2. Select **Publish now** on the reference PC, or **Check now** on a follower. A follower also reads the folder again every 60 seconds.

**Collect:** `Foundry.log`.

## "This folder holds files that are not a publication of this PC. Choose an empty folder." <a href="#not-a-publication" id="not-a-publication"></a>

**Where:** **Publish to a folder** dialog, or status of the reference PC, for example after **Publish now**. In the **Follow a folder** dialog, the form is "This folder does not hold a published configuration.".

**Cause:** The reference PC publishes only to an empty folder or to a folder it already publishes to. A follower was given a folder where no reference PC has published yet, or the wrong folder.

**Fix:** On the reference PC, choose an empty folder. On a follower, check the path with the administrator of the reference PC.

**Collect:** Nothing.

## "Another PC publishes to this folder. Take it over to publish from this PC." <a href="#another-pc-publishes" id="another-pc-publishes"></a>

**Where:** Status of the reference PC, or the **Take over this folder** dialog after **Publish**.

**Cause:** A version of the folder is signed by another PC, which took the folder over or publishes to it.

**Fix:** Agree with your colleagues which PC is the reference. To publish from this PC, [take the folder over](../../foundry-osd/configuration-sharing/publish-to-a-folder.md#take-over-a-folder). Otherwise select **Stop publishing**.

**Collect:** Nothing.

## "The folder is now published by another PC. Accept the new reference or stop following." <a href="#new-reference" id="new-reference"></a>

**Where:** Status of a follower.

**Cause:** Another PC took the folder over, or the reference PC took it over again after its keys were lost. Nothing is applied from that PC until you accept it.

**Fix:** If you expect the change, [accept the new reference](../../foundry-osd/configuration-sharing/follow-a-folder.md#accept-a-new-reference). If nobody announced it, select **Stop following** and contact the administrator of the reference PC.

**Collect:** Nothing.

## "The publication password does not open the current publication. Enter the new password." <a href="#wrong-password" id="wrong-password"></a>

**Where:** Status of a follower. Second forms: "The publication password does not open the publication of this PC in this folder." on the reference PC, "This password does not open the publication, or the publication was modified." in the **Follow a folder** and **Enter publication password** dialogs, and "This password does not open the current publication." in **Take over this folder**.

**Cause:** The reference PC changed the password or reset the publication, the password was mistyped, or, on the reference PC, this PC published to the folder before with another password.

**Fix:** On a follower, select **Enter password** and type the new password. On the reference PC, publish with the password used before for this folder, or choose an empty folder.

**Collect:** Nothing.

## "Passwords are needed: enter the publication password, or enter the missing passwords on the configuration pages." <a href="#passwords-needed" id="passwords-needed"></a>

**Where:** Status of a follower. On the reference PC, the status is "The publication password or keys of this PC could not be read."

**Cause:** Windows could not open the saved passwords of this PC, for example after an administrator reset your Windows password: see [When passwords cannot be restored](../../foundry-osd/configuration-sharing/saved-passwords.md#when-passwords-cannot-be-restored). On a follower, the publication may also leave out passwords that this PC does not have yet.

**Fix:**

- On a follower, select **Enter password** and type the publication password, then type the missing passwords on the pages marked **Needs attention** on **Start**.
- On the reference PC, its publication keys are lost. Select **Take over** and type the current publication password: this PC publishes again with new keys, and each follower [accepts it as the new reference](../../foundry-osd/configuration-sharing/follow-a-folder.md#accept-a-new-reference) once. Without the publication password, select **Stop publishing**, publish to a new empty folder, and have the followers follow it.

**Collect:** `Foundry.log`.

## "The publication was modified. It is not applied." <a href="#tampered" id="tampered"></a>

**Where:** Status of a follower.

**Cause:** The current version in the folder does not match the signature of the reference PC: a file was damaged, changed by hand or by a synchronization conflict. The follower keeps the version it applied last.

**Fix:** On the reference PC, make a change to the configuration: a new version is published a few seconds later. If the status comes back, find out who or what writes in the folder.

**Collect:** `Foundry.log` of the follower.

## "The folder points to an older version than the one applied. It is ignored." <a href="#rollback-rejected" id="rollback-rejected"></a>

**Where:** Status of a follower.

**Cause:** The folder was restored from a backup or a snapshot, or a synchronized copy older than the one this PC read replaced it. A follower never goes back to an older version.

**Fix:** On the reference PC, make a change to the configuration: the new version is newer than the one applied, and followers take it.

**Collect:** Nothing.

## "This publication requires a newer version of Foundry OSD." <a href="#unsupported" id="unsupported"></a>

**Where:** Status of a follower, or the **Follow a folder** dialog. For an exported file, the form is "This file requires a newer version of Foundry OSD." in **Import configuration**.

**Cause:** The reference PC, or the PC that exported the file, runs a newer Foundry OSD.

**Fix:** [Update Foundry OSD](../../foundry-osd/settings.md#update-app) on this PC. Update every follower together with the reference PC.

**Collect:** The Foundry OSD version of both PCs, from **About**.

## "Checking the folder: the publication has not fully arrived yet and is read again at the next check." <a href="#checking" id="checking"></a>

**Where:** Status of a follower. In the **Follow a folder** dialog the form is "The publication has not fully arrived in this folder yet. Try again in a moment.".

**Cause:** The folder points to a version whose files are not all there yet, usually because a OneDrive or SharePoint folder is still synchronizing.

**Fix:** Wait. The follower reads the folder again every 60 seconds. If the status stays, check the synchronization client on this PC and on the reference PC.

**Collect:** Nothing.

## "The configuration is applied, but some shared content could not be installed." <a href="#content-failed" id="content-failed"></a>

**Where:** Status of a follower, with "Items that could not be installed: \<names\>. They are fetched again when the reference PC publishes a new version." under it. Second form: "The configuration is applied, but some shared content is still not available after repeated checks.", with "The content may be damaged in the folder. Publish again from the reference PC." Media creation stays blocked. The readiness bar of **Start** reads **Shared content could not be installed** with "Some shared content could not be installed on this PC. It is fetched again when the reference PC publishes a new version. Check Configuration sharing on the Settings page.", or **Shared content is not available** with "Some shared content is still not available after repeated checks. Publish again from the reference PC, then check Configuration sharing on the Settings page."

**Cause:** A post-installation package, driver set or image could not be downloaded or imported: not enough free space, a damaged file in the folder, or a driver set that breaks the [limits of Custom drivers](../../foundry-osd/customization/custom-drivers.md#limits).

**Fix:**

1. Free space on the drive of `%LocalAppData%\Foundry`.
2. On the reference PC, make a change to the configuration so that it publishes a new version. The follower downloads the missing content again.

**Collect:** `Foundry.log` of the follower.

## "Publishing and following are unavailable during this session because the sharing state could not be read. See the log for details." <a href="#sharing-unavailable" id="sharing-unavailable"></a>

**Where:** Card of either role.

**Cause:** Foundry OSD could not read `%LocalAppData%\Foundry\Configuration\sharing.json`, which records whether this PC publishes or follows.

**Fix:** Restart Foundry OSD. If the message comes back, check the access of your Windows account to that folder.

**Collect:** `Foundry.log`.

## Export and import messages <a href="#export-and-import-messages" id="export-and-import-messages"></a>

**Where:** **Export configuration** and **Import configuration** dialogs, and the card after they close. **Collect:** `Foundry.log` for the last two rows.

| Message | Cause and fix |
| --- | --- |
| "Wrong passphrase or damaged file." | Type the passphrase again. If it is right, the file is damaged: export it again. |
| "This file is not a Foundry configuration, or it is damaged." | Choose a `.foundryconfig` file, or a `.foundryprofile` file of [Settings backup and sync](../../foundry-osd/configuration-sharing/migration.md). |
| "The file could not be read." | Check that the file exists and that you can open it. |
| "Import is not available while this PC follows a folder. Stop following first." | Import is refused on a follower. Select **Stop following** first if this PC must leave the folder. |
| "Configuration imported, but its passwords could not be saved. Enter them again on the configuration pages." | Windows could not save the passwords: type them on their pages. |
| "The configuration was exported, but some files are missing or damaged on this PC and were not included (count: \<number\>). ..." | Select those files again on their pages, then export again. |
| "The files of this configuration exceed 16 MiB in total and cannot be exported." | Remove answer files or certificates that the configuration does not need. |
| "The configuration could not be exported. Check the destination and try again." | Choose a folder where you can write. |
| "The configuration could not be imported. The current configuration is unchanged." or "The operation could not be completed. See the log for details." | Read the reason in the log. |
