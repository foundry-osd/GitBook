# Settings backup and sync troubleshooting

Use this page for a message shown under the **Settings backup and sync** card of **Settings** in Foundry OSD, or in its export, import, share and connect dialogs; the rest of the application is in [Foundry OSD application troubleshooting](../foundry-osd.md).

**Collect** names the Foundry OSD log, `%ProgramData%\Foundry\Logs\Foundry.log`: see [Log locations](../logs-and-support.md#log-locations). Never send a connection file, an exported configuration or their passwords.

| What you see | Go to section |
| --- | --- |
| "Couldn't complete this action. Check the file, its password, your access permissions, and your network connection." | [Couldn't complete this action](#could-not-complete) |
| "Foundry can't open this settings format. Update Foundry and try again." | [Settings format](#settings-format) |
| "Windows cannot unlock these saved settings ...", or **Synchronize** became **Restore access** | [Cannot unlock](#cannot-unlock) |
| "Some passwords or source files are missing. ...", also in a dialog when you close Foundry OSD | [Passwords or files missing](#missing-passwords) |
| "The shared folder cannot be reached. ..." or "Another operation is using this shared folder. ..." | [Folder cannot be reached](#folder-unreachable) |
| "The saved versions in the shared folder have changed unexpectedly. ..." | [Saved versions changed](#versions-changed) |
| "This folder name is already in use. ..." or "Choose a new, empty subfolder ..." | [Folder name in use](#folder-in-use) |
| "These shared settings were deleted. ..." | [Shared settings deleted](#shared-deleted) |
| "Some old saved passwords or access keys could not be removed. ..." | [Old passwords not removed](#cleanup-pending) |
| "Check the required fields. ...", "Use a name without special characters ..." or "The passwords do not match." | [Required fields](#required-fields) |
| "\<number\> files are missing or were not included. ..." | [Files missing after import](#files-missing) |

## "Couldn't complete this action. Check the file, its password, your access permissions, and your network connection." <a href="#could-not-complete" id="could-not-complete"></a>

**Where:** After an import, an export, a connection or a synchronization, under the **Settings backup and sync** card. Sync status: **Unable to synchronize**.

**Cause:** One message covers several causes. The most frequent:

- The file password or the connection password is wrong.
- The file is damaged, or is larger than 16 MiB.
- A file of the wrong kind was chosen: an exported configuration where a connection file is expected, or the connection file of another shared configuration.
- A file selected in the configuration, such as an answer file or a certificate, is larger than 4 MiB.
- The connection file was saved inside the shared folder.

**Fix:**

1. Check which file you chose and type its password again.
2. Check that you can read and write in the shared folder.
3. Read the reason in the log.

**Collect:** `Foundry.log`. Search for "Profile operation failed"; the line names the operation and the reason.

## "Foundry can't open this settings format. Update Foundry and try again." <a href="#settings-format" id="settings-format"></a>

**Where:** After an import, a connection or a synchronization, under the **Settings backup and sync** card. Sync status: **Unable to synchronize**.

**Cause:** The file or the shared configuration was written by a newer Foundry OSD than the one on this workstation.

**Fix:** Update Foundry OSD on this workstation, then try again. In a team, update every workstation: as soon as one updated workstation synchronizes, the others cannot until they are updated too.

**Collect:** The Foundry OSD version of both workstations, from **About**.

## "Windows cannot unlock these saved settings or their synchronization access for your account on this PC." <a href="#cannot-unlock" id="cannot-unlock"></a>

**Where:** When a configuration is selected or synchronized, under the **Settings backup and sync** card. Sync status: **Unable to synchronize**, and the **Synchronize** button becomes **Restore access**.

**Cause:** The key that protects the configuration in Windows Credential Manager is missing: another Windows account is signed in, saved credentials were cleaned, or the data was copied from another PC.

**Fix:**

1. Sign in with the Windows account that created the configuration.
2. For a shared configuration, select **Restore access** and choose its connection file.
3. Otherwise, import the configuration again from an exported file.

**Collect:** `Foundry.log`.

## "Some passwords or source files are missing. Add them before saving or sharing passwords." <a href="#missing-passwords" id="missing-passwords"></a>

**Where:** Under the **Settings backup and sync** card, with the sync status **Needs attention**, and in a dialog titled **Foundry settings** when you close Foundry OSD.

**Cause:** **Remember passwords** is on, or the configuration is shared with its passwords, and a password or a file that a feature requires has not been provided.

**Fix:** Complete the page concerned, or turn **Remember passwords** off. In the closing dialog, **Cancel** returns to the app so that you can complete it; **Close** quits and keeps the last saved state.

**Collect:** Nothing.

## "The shared folder cannot be reached. Check its address and your network access. Your changes are kept on this PC." <a href="#folder-unreachable" id="folder-unreachable"></a>

**Where:** During a synchronization, under the **Settings backup and sync** card. Sync status: **Folder unavailable**. A second form is "Another operation is using this shared folder. Try again shortly. Your changes are kept on this PC.".

**Cause:**

- The network, the VPN or the file server is not available.
- Your account is not allowed to read and write in the folder. Foundry OSD reports a refused access the same way.
- Another workstation is synchronizing at this moment.

**Fix:**

1. Open the folder in File Explorer with the same account and check that you can create a file in it.
2. Select **Synchronize** again after a moment.

**Collect:** `Foundry.log`.

## "The saved versions in the shared folder have changed unexpectedly. Restore from a backup you trust." <a href="#versions-changed" id="versions-changed"></a>

**Where:** During a synchronization, under the **Settings backup and sync** card. Sync status: **Unable to synchronize**.

**Cause:** Files of the shared folder were deleted, or replaced by older copies.

**Fix:**

1. Restore the shared folder from the most recent backup you trust.
2. If you have no backup, choose the workstation that holds the right settings, select **Disconnect** on it, then share the configuration again in a new, empty folder and give the new connection file to the other workstations.

Do not edit or delete files in the shared folder by hand.

**Collect:** `Foundry.log`.

## "This folder name is already in use. Connect using a connection file, or go back and choose another name." <a href="#folder-in-use" id="folder-in-use"></a>

**Where:** **Share this configuration** dialog. A second form is "Choose a new, empty subfolder in the shared folder. Existing files will stay unchanged.".

**Cause:** The folder that would be created for this configuration already exists, or the folder you chose is not empty.

**Fix:** To join the configuration that already uses this name, select **Connect** and choose its connection file. To share a new one, select **Choose another name**.

**Collect:** Nothing.

## "These shared settings were deleted. Choose Duplicate to keep your changes on this PC." <a href="#shared-deleted" id="shared-deleted"></a>

**Where:** Under the **Settings backup and sync** card. Sync status: **Needs attention**.

**Cause:** Someone deleted the shared configuration with **Also delete for everyone**.

**Fix:** Select **More options** > **Duplicate** to keep a local copy, then remove the old entry with **Delete from this PC**.

**Collect:** Nothing.

## "Some old saved passwords or access keys could not be removed. Try again when Windows Credential Manager is available." <a href="#cleanup-pending" id="cleanup-pending"></a>

**Where:** Under the **Settings backup and sync** card. Sync status: **Needs attention**.

**Cause:** Windows Credential Manager did not answer while Foundry OSD was removing saved passwords.

**Fix:** Try the same action again later, or after restarting Windows.

**Collect:** `Foundry.log`.

## "Check the required fields. If a network folder is requested, use a path such as \\\\server\\share\\folder. If you enter a file password twice, make sure both entries match." <a href="#required-fields" id="required-fields"></a>

**Where:** Export, import, share and connect dialogs of **Settings backup and sync**. Related messages: "Use a name without special characters or a trailing dot or space." and "The passwords do not match.".

**Cause:**

- A name or a password is empty.
- The shared folder is given as a mapped drive letter such as `Z:\Foundry`. Only a network path starting with `\\` is accepted.
- The configuration name cannot be used as a folder name.
- The two passwords differ.

**Fix:** Correct the field. For the shared folder, type the network path, for example `\\server\share\Foundry`.

**Collect:** Nothing.

## "\<number\> files are missing or were not included. Select their locations before creating media." <a href="#files-missing" id="files-missing"></a>

**Where:** **Settings backup and sync**, in the preview shown when you import a configuration or connect to a shared one.

**Cause:** The configuration refers to files, such as certificates or answer files, that were not included in the export or are not on this workstation.

**Fix:** Finish the import, then open each page marked **Needs attention** and select the file again.

**Collect:** Nothing.
