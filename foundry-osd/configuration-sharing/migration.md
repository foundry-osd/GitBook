# Move from Settings backup and sync

Configuration sharing replaces **Settings backup and sync** and its deployment profiles. The first time Foundry OSD starts with Configuration sharing, it reads the old profile store, `%LocalAppData%\Foundry\Profiles`, and moves the configuration you were using into the new storage, once for each Windows account. Nothing is deleted.

## What is moved by itself

| Item | Comes from |
| --- | --- |
| The options of every page | The options as you last left them |
| Passwords and copies of files | The profile that was in use, when your Windows account can still open it |
| Files that the profile did not hold | Their original location, when the file is still there |

Post-installation packages, custom driver sets and custom Windows images stay in their libraries and need nothing.

**Home** then shows a notice for each item that needs you. Close a notice once it is handled: it stays closed. The passwords notice comes back at each start while the saved passwords still cannot be read: see [When passwords cannot be restored](saved-passwords.md#when-passwords-cannot-be-restored).

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-configuration-sharing-migration-01-home-notices.png`
- **Capture:** Show **Home** after the first-launch migration with the notices about passwords, files and synchronization.
{% endhint %}

| Notice on Home | What to do |
| --- | --- |
| "Saved passwords could not be restored. Enter them again on the configuration pages." | Type the passwords on their pages. This happens when the profile cannot be opened by your Windows account. |
| "Some files used by the configuration could not be recovered. Select them again on the configuration pages." | Select each file again on the page marked **Needs attention** on **Start**. |
| "The configuration synchronization of earlier versions is no longer supported. To share this configuration, use Publish to a folder on the reference PC." | The profile was synchronized through a shared folder. Set up the PCs again, as described below. |

## Synchronized profiles

Old shared folders cannot be followed. On the PC whose configuration the others must use, [publish to a folder](publish-to-a-folder.md), preferably a new empty one. On each other PC, [follow that folder](follow-a-folder.md).

## Other profiles of this PC

Only the profile in use is moved. To use another one:

1. In **Settings**, expand **Configuration sharing** and select **Import** on **Import configuration**.
2. Under **Previous configurations on this PC**, select the profile and select **Open**.
3. Read the preview and select **Import**. It replaces the current configuration.

A profile whose key is no longer in Windows Credential Manager, for example after saved credentials were cleaned, shows "This configuration cannot be opened by this Windows user."

## Exported profiles

**Import configuration** also accepts the `.foundryprofile` files exported by Settings backup and sync: select the file under **File**, type the file password in **Passphrase**, and continue as for any [import](export-and-import.md#import-a-configuration). Such a file carries no post-installation package, driver set or image: those that this PC does not have are listed under **Missing on this PC**.

A `Connection.foundryprofile` connection file imports the configuration it holds and shows the notice that synchronization is no longer supported. It does not connect this PC to any folder.

## What stays on the disk

The old profile store, `%LocalAppData%\Foundry\Profiles`, the keys of its profiles in Windows Credential Manager and the old shared folders are left as they are. Foundry OSD no longer writes them, and reads the store and its keys only for **Previous configurations on this PC**.

## Related

- [Configuration sharing](../configuration-sharing.md)
- [Saved passwords and files](saved-passwords.md)
