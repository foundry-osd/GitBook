# Sync and conflicts

Once a configuration is [shared or connected](../deployment-profiles.md#create-a-shared-profile), the **Settings backup and sync** card shows its status next to **Synchronize**.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-deployment-profiles-sync-01-conflict.png`
- **Capture:** Show the expanded card of a shared demonstration configuration with the status **Choose a version** and the conflict bar under it.
{% endhint %}

## When changes are sent and applied

- **Synchronize** checks at once: it sends your changes, or applies those of the other PCs.
- **Automatic sync**, on after sharing or connecting, checks at startup and every 30 seconds. It waits 2 seconds after your last change, and while a dialog is open or media is being created.
- Changes from another PC are applied by themselves only at startup, before you change anything, or while **Settings** is open. Elsewhere the status reads **Changes available**.
- The ISO output path, the custom driver folder and the telemetry choices are never synchronized.
- When passwords and confidential files are not shared, each PC keeps its own, as long as the account or the certificate they belong to does not change.

## Status next to Synchronize

| Status | Meaning | What to do |
| --- | --- | --- |
| **Up to date**, **Synchronizing…**, **Ready to synchronize** | The last check succeeded, a check is running, or there is no result yet. | Nothing |
| **Changes to synchronize** | Changes of this PC are waiting. | Wait, or select **Synchronize** |
| **Changes available** | Another PC published changes. | Open **Settings**, or select **Synchronize** |
| **Choose a version** | This PC and another one both changed the configuration. | [Resolve the conflict](#resolve-a-conflict) |
| **Folder unavailable** | The shared folder cannot be reached, or another PC is writing to it. | Check the network and your rights on the folder |
| **Needs attention**, **Unable to synchronize** | Something is missing, or the check failed. | Read the message under the card |
| **Not configured** | The configuration is not shared. | Nothing |

Your changes stay on this PC whatever the status, and are sent at the next check that succeeds. Each message under the card has its entry in [Settings backup and sync troubleshooting](../../troubleshooting/foundry-osd/settings-backup-and-sync.md).

## Resolve a conflict

A bar under the card reads "These settings were changed on this PC and by someone else. Choose which version to keep." The choice applies to the whole configuration: agree with your colleague first.

| Button | Result |
| --- | --- |
| **Use the shared version** | Replaces the options of this PC with the shared ones |
| **Use this PC's version** | Publishes the options of this PC to the other PCs |
| **Duplicate** | Keeps the options of this PC as a separate configuration that is not shared |

## Restore access

When Foundry OSD has lost its access to the shared folder, **Synchronize** becomes **Restore access**. Select it, choose the connection file of this configuration and type its password. Your options are kept.

Keep a spare connection file with **More options** > **Save connection file**, outside the shared folder.

## Disconnect or delete

**Disconnect** stops synchronization on this PC only. Your options, the shared folder and the other PCs are not changed.

**More options** > **Delete from this PC** removes the configuration from this PC. **Also delete for everyone** deletes the shared one too, once this PC has synchronized at least once. The other PCs then read "These shared settings were deleted. Choose Duplicate to keep your changes on this PC."

{% hint style="warning" %}
After a deletion, no configuration is selected: the pages show default values and what you change is not saved. Choose a configuration in the list, or select **More options** > **Duplicate**, before you edit anything.
{% endhint %}

## Look after the shared folder

- Foundry OSD keeps the 20 most recent versions of the configuration and removes older ones itself.
- Do not delete or edit files in the configuration folder, and do not roll it back to an earlier state, for example from a server snapshot. A PC stops with "The saved versions in the shared folder have changed unexpectedly. Restore from a backup you trust." when the version it last synchronized is no longer in the folder. Restore the folder from a backup that still contains that version, that is, one made after that PC's last synchronization. Without such a backup, select **Disconnect** on the PC that holds the options to keep, and share again under a new name, because the old folder still exists.
- Update Foundry OSD on every PC together. Once a newer version that changes the settings format publishes, older ones read "Foundry can't open this settings format. Update Foundry and try again."
- Foundry OSD has no command to withdraw someone's access: access rests on the connection file, its password and the rights on the folder. As a precaution when someone leaves, share again under a new name, have the old folder removed, and change the passwords that were shared.

## Related

- [Settings backup and sync](../deployment-profiles.md)
- [Export and import](export-and-import.md)
- [Settings backup and sync troubleshooting](../../troubleshooting/foundry-osd/settings-backup-and-sync.md)
