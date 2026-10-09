# Settings backup and sync

A configuration is the whole set of options you choose in Foundry OSD. The **Settings backup and sync** card of **Settings** keeps several configurations under different names, copies one to another PC as an encrypted file, and synchronizes one between PCs through a shared network folder.

With one PC and one set of options, skip this page: Foundry OSD creates a configuration named **Default** at its first start and saves your changes into it. Some screens of the app call the configuration a profile.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-deployment-profiles-01-card.png`
- **Capture:** Show the expanded **Settings backup and sync** card on the **Settings** page with a shared demonstration configuration selected: the configuration list, **More options**, **Import**, **Export**, **Synchronize** with its status and **Disconnect**, **Automatic sync**, and **Remember passwords** with **Clear saved passwords and access**.
{% endhint %}

## Create and select a configuration

1. Open **Settings**, at the bottom of the navigation pane, and find the **Settings backup and sync** card.
2. Select **More options** > **Duplicate**, type a **Configuration name** of at most 120 characters, and select **Continue**. The copy becomes the configuration in use.
3. To change configuration later, choose it in the list of the card. Foundry OSD saves your pending changes, then loads the options of that configuration on every page.

Changes are saved by themselves within a second, and when you close the app. **Duplicate**, **Import** and connecting to a shared configuration are the only ways to add a configuration.

The ISO output path, the custom driver folder and the telemetry choices belong to the PC, not to a configuration. Custom Windows images and post-installation packages also stay on each PC: a configuration only refers to them.

## What is remembered

| Control | What it does |
| --- | --- |
| **Remember passwords** | Saves the passwords and the confidential files you selected (answer files, network profiles, certificates) for your Windows account on this PC. On by default. When off, they are lost when you close the app. |
| **Include passwords and confidential files** | Offered when you export or share. Puts them in the exported file or the shared folder. |
| **Clear saved passwords and access** | Removes the saved passwords, the saved confidential files and the access to the shared folder, and turns off **Remember passwords** and **Automatic sync**. The options stay. Then type the passwords and select the files again, and use **Restore access**. |

Passwords of the join accounts of [zero-touch Domain Join](domain-join/zero-touch.md) are never written to an exported file. A shared configuration carries them only when **Include passwords and confidential files** was selected when it was shared. Size limits are in [Export and import](deployment-profiles/export-and-import.md).

## Share this configuration <a href="#create-a-shared-profile" id="create-a-shared-profile"></a>

The shared folder must meet these requirements:

- It is a network folder written as `\\server\share`. A mapped drive letter such as `Z:\` is refused, even when **Browse** returns it.
- Every PC can read and write it with the Windows account of its user.
- It uses SMB 3 with encryption required. Foundry OSD does not check this and accepts a share without it: it is your responsibility.
- It is not synchronized by a cloud service such as OneDrive or Dropbox.
- Foundry OSD creates `\\server\share\Foundry\<configuration name>` in it. That subfolder must not exist yet. The name cannot contain special characters or end with a dot or a space.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-deployment-profiles-02-share-dialog.png`
- **Capture:** Show the **Share this configuration** dialog filled in with a demonstration name and a `\\server\share` path, the destination line under the folder field, both password fields, the two check boxes and the **Learn more** link.
{% endhint %}

1. Select the configuration, expand the card, and select **Set up synchronization…** > **Share this configuration** > **Continue**.
2. Check the **Configuration name**, type the **Shared folder** or select **Browse**, and read the destination shown under the field.
3. Type a **Connection password** and repeat it in **Confirm password**. Any password of 1 to 1,024 characters is accepted: choose a long one.
4. Decide on **Include passwords and confidential files**. The choice is made once for every PC that connects; to change it, disconnect and share again under another name.
5. Select **Remember this connection on this PC** to synchronize after a restart without the connection file.
6. Select **Share**. Foundry OSD writes `Connection.foundryprofile` in the new folder. Give your colleagues this file, and its password by another channel.

## Connect another PC

1. On the other PC, select a configuration that is not shared, then **Set up synchronization…** > **Connect to a shared configuration** > **Continue**.
2. Select `Connection.foundryprofile` and type the **Connection password**.
3. Check the **Shared folder**, filled in from the file. It is the folder of the configuration, such as `\\server\share\Foundry\Paris`.
4. Read the preview, choose **Remember passwords** and **Remember this connection on this PC**, then select **Continue**.

The shared configuration is added to the list and becomes the one in use.

## Related

- [Export and import](deployment-profiles/export-and-import.md)
- [Sync and conflicts](deployment-profiles/sync-and-conflicts.md)
- [Security and credentials](../reference/security-and-credentials.md)
- [Foundry OSD application troubleshooting](../troubleshooting/foundry-osd.md)
