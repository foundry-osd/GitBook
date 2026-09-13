# Deployment profiles

Use [Settings > Settings backup and sync](settings.md#settings-backup-and-sync) to save named deployment configurations, transfer them to another PC, and keep settings synchronized with your team.

Choose the configuration you want to use from the list at the top of the card. Foundry saves your pending changes and applies your selection immediately. Expand the card to find **Import**, **Export**, **Synchronize**, and **Remember passwords**. Use **More options** to rename, duplicate, or delete a configuration.

A profile contains your deployment settings and, depending on your choices, passwords and selected files. Changing profiles replaces the settings you are currently editing. It does not change media you have already created.

## Create and select a local profile

1. Configure your deployment in Foundry OSD.
2. Open **Settings > Settings backup and sync** and choose **More options > Duplicate**.
3. Enter a **Configuration name** and continue. Foundry selects the new copy.
4. To use another profile, choose it from the configuration list.

**Duplicate** creates an independent copy that is not connected to synchronization. It keeps the original profile's **Remember passwords** choice. When enabled, available passwords and selected confidential files are saved with the copy.

Foundry saves changes automatically after a short pause and when you close the app. New local profiles start with **Remember passwords** enabled; existing profiles keep their saved choice.

If Foundry asks you to complete a password or select a missing file, do so before switching profiles or closing the app. Your current changes stay on screen, and the last successfully saved settings remain available. A new profile can start with incomplete inputs, but later saves with **Remember passwords** enabled require those inputs to be complete.

If saving or switching fails, Foundry keeps the current profile selected. Resolve the reported problem and try again. If saving fails while closing, cancel closing to fix it; choosing **Close** leaves you with the last successfully saved settings.

Before creating media, review [media readiness](media/README.md#review-readiness) for files, drivers, or passwords that need attention on this PC.

## Choose what to remember and share

These options control different things:

| Option | What it does |
| --- | --- |
| Remember passwords | Saves supported deployment passwords and selected confidential files with this profile on this PC. |
| Include passwords and confidential files | Includes available passwords and selected confidential files when you export or share the configuration. |
| Remember this connection on this PC | Lets Foundry reconnect to the shared configuration after restarting, without asking you to restore access. |

Turning **Remember passwords** off stops saving passwords with future changes. Passwords already entered remain usable until the current session ends. Your separately remembered synchronization connection is also kept. To clear both, use **Clear saved passwords and access** beside the switch.

Saved profiles are protected for your Windows account on this PC using Windows Credential Manager. To move a profile to another PC or Windows account, use **Export** or connect through the shared configuration's connection file. Copying Foundry's local data folder is not a supported transfer method.

When Foundry first brings your existing settings into a profile, **Remember passwords** starts enabled and saves supported passwords and selected files that are available. An existing profile's choice is preserved.

Supported passwords include Wi-Fi, Protected deployment, OOBE local accounts, and selected network or Autopilot PFX certificates. Complete required fields and matching confirmations. For an intentionally blank OOBE password, use its explicit password choice. Missing passwords are not filled from another profile.

Microsoft sign-in sessions are not transferred. Sign in again when an Autopilot task requires it. An imported Autopilot configuration with a valid saved registration can use its matching PFX certificate without a new sign-in. If that file was omitted, select the matching certificate and enter its password on this PC. A different or expired certificate must be resolved before creating media.

[Desktop proxy credentials](settings.md#proxy) are managed separately and do not configure Windows PE.

### Clear saved passwords and access

Choose **Clear saved passwords and access** and confirm. Foundry keeps this profile's settings, clears its saved and currently entered passwords, and stops synchronization by clearing its saved connection access. Re-enter required passwords before creating media. Use **Restore access** when you want to reconnect.

If Foundry reports that cleanup is incomplete, resolve the reported problem and retry. Previously saved data may remain until cleanup succeeds.

This action does not remove exported files, copies on the shared folder, original source files, or existing deployment media.

## Export an encrypted package

1. Select the profile you want to transfer.
2. Choose **Export** and select where to save the `.foundryprofile` file.
3. Enter a **File password** and repeat it in **Confirm file password**.
4. Choose whether to **Include passwords and confidential files**, then continue.

With the option off, the file contains settings without saved passwords or attached confidential files. Autopilot JSON entered in the configuration is still part of those settings; review it before sharing.

With the option on, Foundry includes available files selected in the configuration, such as custom answer files, wired or enterprise Wi-Fi XML profiles, and network or Autopilot PFX certificates. Missing files cannot be included.

An export supports up to **64 attached files**, **4 MiB per file**, and **8 MiB of attached files in total**. The complete configuration, including attachments, must fit within **16 MiB**. Transfer Windows images, driver collections, and generated media separately.

Send the file password through a separate trusted channel. Anyone who can open the export can read the passwords and confidential files it includes.

## Preview and import a copy

1. Choose **Import** and select a `.foundryprofile` file. This file type is selected by default; **All files** is also available.
2. Enter its **File password**.
3. Review the configuration name, included-file and password counts, and any missing-file notices.
4. Review **Remember passwords** and confirm that you want to use the imported settings.

**Remember passwords** is selected by default for a complete import. If passwords or files are missing, leave it unchecked, import the settings, supply the missing inputs, and then enable it.

Import creates an independent local copy. To keep it synchronized with a shared configuration, use **Set up synchronization… > Connect to a shared configuration** instead, even if the file you selected is a connection file.

After importing, check source files, output locations, custom driver folders, and the target disk before creating media. PC-specific locations and disk selections are not transferred. Reselect any files that were omitted from the export.

If the password is wrong or the file is damaged or unsupported, Foundry leaves your current configuration in place. Check the password or obtain a fresh export and try again.

## Create a shared profile

Choose a network folder that all participating PCs can read and write, such as `\\server\share`. It can already contain files. Foundry creates a separate folder for your configuration at `\\server\share\Foundry\<configuration name>`.

The shared folder must use **SMB 3 with encryption required**. Ask your administrator to configure this and restrict access to your team. Foundry uses your Windows account's access; it does not change the folder permissions or server settings.

OneDrive, Dropbox, and other cloud-synchronized folders are not supported. If you use a NAS, confirm compatibility with your administrator and test synchronization between your PCs before relying on it.

1. Select the local profile, choose **Set up synchronization…**, then **Share this configuration**.
2. Enter a **Configuration name**. The current name is filled in for you.
3. Enter the parent shared folder or choose **Browse**. Check the destination shown below the field.
4. Enter a **Connection password** and repeat it in **Confirm password**. Other PCs will need it to connect.
5. Choose whether to **Include passwords and confidential files**. People using this configuration can read any passwords and files you include.
6. Choose whether to **Remember this connection on this PC**, then select **Share**.
7. Foundry creates `Connection.foundryprofile` in the new folder. Give your team access to this file and send its password separately.

If Foundry asks for missing passwords or files, complete them before sharing confidential inputs.

If that configuration folder already exists, choose **Connect** and select its connection file, or choose **Choose another name**. Existing files are not overwritten. Renaming the configuration later does not move its shared folder.

If sharing is interrupted, restore access to the folder and choose **Synchronize** on the same profile. Keep the folder and Foundry's local data intact while retrying. If you did not choose **Remember this connection on this PC**, keep Foundry open until the connection file is available; otherwise, you may be unable to reconnect after restarting.

## Connect another PC

1. On the other PC, choose **Set up synchronization… > Connect to a shared configuration**.
2. Select `Connection.foundryprofile` from the shared configuration folder or a protected copy supplied by your team.
3. Enter its **Connection password**.
4. Check the full configuration folder, such as `\\server\share\Foundry\Deployment - Paris`, and continue. Foundry fills this in when the file contains it.
5. Review the current shared settings, included-file and password counts, and remembering options.
6. Continue to use the reviewed configuration. If another PC changes it before you finish, Foundry shows an updated preview for you to confirm.

As with importing, **Remember passwords** is selected for complete configurations. If inputs are missing, connect first, supply them, and then enable it. Choose **Remember this connection on this PC** separately to reconnect automatically after restarting.

If the suggested folder cannot be reached, browse to the connection file through a server address that works on this PC and check the suggested folder again. You can correct the folder before continuing. Select the configuration's own folder, not its parent share.

If you are already synchronizing with another configuration, choose **Disconnect** before setting up the new connection.

### Keep a recovery copy

The connection file also lets you restore access later. While connected, choose **More options > Save connection file**, enter a file password, and save the extra copy **outside the shared configuration folder**. Keep the copy and its password somewhere your team can recover them if the shared folder becomes unavailable.

Choose **Restore access** and provide the matching connection file and password if Foundry can no longer reconnect. Your current local settings are kept.

If you leave **Remember this connection on this PC** unchecked, keep the connection file and password available: you may need **Restore access** after restarting or switching profiles.

## Synchronize and resolve conflicts

Turn on **Automatic sync** to check for changes at startup and every **30 seconds** while Foundry is running. Checks run in the background. You can also choose **Synchronize** whenever you need a check. Turning automatic sync off still allows local editing and manual synchronization.

Foundry saves your edits locally after a short pause. Automatic checks wait while you are editing or a dialog or operation is running. ISO output paths, custom driver folders, and telemetry or diagnostic preferences stay on this PC; changing only those settings does not create a shared update.

At startup, with automatic sync enabled and the connection available, Foundry can apply shared changes before you begin editing, including from Home. After startup, applying shared changes automatically waits until you open **Settings** and finish any profile dialog or media operation. The card can remain collapsed. This prevents shared changes from replacing settings while you are working on another page.

Check the status beside the synchronization buttons:

| Status | What to do |
| --- | --- |
| Up to date — green | The last check succeeded and no local changes are waiting to be shared. Continue working. |
| Synchronizing… | Wait for the check to finish. |
| Changes to synchronize | Local edits are waiting. Leave automatic sync enabled or choose **Synchronize**. |
| Changes available | Open **Settings** to allow the shared update to apply, or choose **Synchronize**. |
| Choose a version — amber | Review the conflict and choose which configuration to keep. |
| Folder unavailable / Needs attention — amber | Follow the message below the card and retry. |
| Unable to synchronize — red | Resolve the reported problem and retry. |
| Not configured / Ready to synchronize | Set up synchronization or run a check. |

If you and another person change the configuration, Foundry keeps your local edits and asks you to choose a version. Coordinate with the other person before continuing:

| Action | Result |
| --- | --- |
| Use the shared version | Replaces this PC's edits with the shared configuration. |
| Use this PC's version | Shares this PC's configuration with the other PCs. |
| Duplicate | Keeps this PC's configuration as a separate local profile without synchronization. |

Your choice applies to the **whole configuration**. Use **Duplicate** first if you want to preserve your edits separately, and review the copy's password-remembering choice. If someone changes the shared configuration again before you finish, Foundry asks you to resolve the new conflict.

When confidential files are not shared, a PC that lacks a certificate does not remove the matching certificate and password already available on another PC. Changing or removing the selected certificate does clear that association. Check each PC's required files and passwords before creating media.

### If synchronization is interrupted

Your changes stay on this PC while the folder is unavailable. Check the folder address and network access, then choose **Synchronize**. If the folder is temporarily busy, wait briefly and try again.

Foundry resumes interrupted synchronization without sending the same update twice. Keep the shared folder and Foundry's local data intact until it finishes. Conflicting edits still require your choice, and turning automatic sync off does not discard changes waiting to be shared.

### Disconnect or delete a configuration

Choose **Disconnect** to stop synchronizing on this PC while keeping its current local settings and password-remembering choice. The shared folder and other PCs are unchanged. You can then use **Set up synchronization…** to connect again or share elsewhere.

Choose **More options > Delete from this PC** to remove the local profile and clear the settings and passwords you are currently using. To delete the shared configuration too, select **Also delete for everyone** and confirm. Other PCs are notified when they synchronize. Duplicate any local work you need to keep before resolving a shared deletion.

### Manage the shared folder

Foundry keeps the **20 most recent saved versions** of the shared configuration and automatically removes older version files. Reaching 20 does not stop synchronization. Other files needed for synchronization remain, so the shared folder's total size can continue to grow. Leave file cleanup to Foundry; do not manually remove files from this folder or replace them with an older backup.

Keep Foundry up to date on every PC using the shared configuration.

To set up fresh shared access, disconnect and share the reviewed configuration under a new name or in a new folder. Give the team the new connection file. Use **Duplicate** first if you also want to keep the original connection.

This leaves the old shared folder available to anyone who still has access. Ask your administrator to remove old permissions or restrict that folder. Changing shared access cannot withdraw files or passwords already copied; replace any exposed passwords or certificates separately.

## Create media from the selected profile

Select the intended profile and resolve any missing passwords or files before starting media creation. Foundry uses the settings and selected files from when you start the build. Later edits or synchronized changes do not alter that running build.

Custom drivers remain local. Keep the selected driver folder focused on the target hardware: it must fit within **2 GiB** and **10,000 files and folders**.

Foundry cleans up its temporary copies after the build. If cleanup is interrupted, it retries when you restart or prepare another build.

Protect the media separately using [Protected deployment](general.md#protected-deployment) and the [deployment media security guidance](../reference/security-and-credentials.md). Recreate media when its included passwords or deployment settings change.
