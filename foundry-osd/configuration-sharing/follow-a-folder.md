# Follow a folder

**Follow a folder** makes this PC use the configuration that a reference PC [publishes to a folder](publish-to-a-folder.md). Foundry OSD applies each new version by itself, and the published settings become read-only on this PC.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-configuration-sharing-follow-01-preview.png`
- **Capture:** Show the **Follow a folder** dialog after **Open**, with the folder, the publication password and the preview: **Reference PC**, **Published on**, **Version**, **Passwords** and **Download size**.
{% endhint %}

## Before you start

- The folder path and the publication password, from the administrator of the reference PC.
- Read access to the folder with your Windows account.
- Nothing to keep in the current configuration of this PC: following replaces it. [Export it](export-and-import.md) first if needed.

## Follow

1. Open **Settings**, expand **Configuration sharing**, and select **Follow** on **Follow a folder**.
2. In **Folder**, type the path or select **Browse**, type the **Publication password**, and select **Open**.
3. Read the preview: **Reference PC**, **Published on**, **Version**, **Passwords** (**Included** or **Not included**) and **Download size**, the size of the content this PC does not have yet.
4. Read the warning "Following replaces the current configuration and makes the published settings read-only. The ISO output path, the additional WinPE driver folder and the telemetry settings of this PC are kept.", then select **Follow**.

The card shows "This PC follows \<PC name\>" and the status "Up to date." or "The configuration is applied. Shared content is being downloaded."

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-configuration-sharing-follow-02-card-follower.png`
- **Capture:** Show the **Configuration sharing** card of a follower: the role with the reference PC name, the **Up to date** status, **Folder**, **Version**, **Last successful check**, and the buttons **Check now** and **Stop following**.
{% endhint %}

## What changes on this PC

Every configuration page shows **Managed by a reference PC**, and its settings are read-only. You can still change:

- the **ISO output** path on **Start**, the **Additional WinPE driver folder** on [General](../general.md), and the telemetry settings of **Settings**;
- the passwords, when the publication does not include them. Type them on their pages: they are [saved](saved-passwords.md) on this PC only. For OOBE local accounts and zero-touch Domain Join, **Edit** opens a dialog where only the password can be entered.

When the publication includes the passwords, the password fields are read-only too, and each new version replaces them.

**Import configuration** is not available while this PC follows a folder.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-configuration-sharing-follow-03-managed-banner.png`
- **Capture:** Show a configuration page of a follower, such as **Wi-Fi**, with the **Managed by a reference PC** banner and its settings disabled.
{% endhint %}

## New versions

Foundry OSD reads the folder when it starts and about once a minute. **Check now** reads it at once. A new version is applied by itself; the **Configuration sharing** card of **Settings** shows the status and the **Version**. A media creation that is already running is not affected.

New post-installation packages, driver sets and images are downloaded, then installed, in the background. The card shows each step with the item name and a percentage: "Downloading \<name\>…", then "Installing \<name\>…".

Until every item is installed, the navigation pane shows these items as **Downloading**, and media creation is blocked: **Create ISO**, **Create USB**, **Update USB**, **Create without cache**, **Recreate without cache** and **Create PXE files** are unavailable. The readiness bar of **Start** reads **Waiting for shared content** with "Shared content is still being downloaded. Media creation is available once the download is complete.", and the **Start** step of **Home** reads **Downloading**. The actions become available again by themselves when the synchronization completes. If some content cannot be installed, media creation stays blocked: see [Shared content missing](../../troubleshooting/foundry-osd/configuration-sharing.md#content-failed). Content that the configuration no longer uses stays on this PC.

## Enter a new publication password

After a reset of the publication, and after a password change once the reference PC publishes its next version, the status reads "The publication password does not open the current publication. Enter the new password."

1. Select **Enter password** on the card.
2. Type the new **Publication password** and select **Save**.

## Accept a new reference

When another PC takes over the folder, the status reads "The folder is now published by another PC. Accept the new reference or stop following." Nothing is applied until you decide.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-configuration-sharing-follow-04-accept-reference.png`
- **Capture:** Show the **Accept new reference?** dialog with **Previous key**, **New key** and **Version**.
{% endhint %}

1. Select **Accept new reference**.
2. Compare **Previous key** and **New key**, and check **Version**. Accept only a change you expect, for example a take over announced by the administrator.
3. Select **Accept**. The new version is applied. If the version of the new reference is older than the one this PC applied, the status then reads "The folder points to an older version than the one applied. It is ignored.": see [Older version](../../troubleshooting/foundry-osd/configuration-sharing.md#rollback-rejected).

If the reference changes again while the dialog is open, it shows "The reference changed while this dialog was open. Review the new key before you accept it." with the new key: review it, then select **Accept** again.

## Stop following

Select **Stop following**. The **Stop following?** dialog reads "The configuration stays as it is and becomes editable on this PC." Select **Forget publication password** to remove it from this PC: following the folder again then asks for it.

## Related

- [Publish to a folder](publish-to-a-folder.md)
- [Configuration sharing](../configuration-sharing.md)
- [Configuration sharing troubleshooting](../../troubleshooting/foundry-osd/configuration-sharing.md)
