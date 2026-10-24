# Publish to a folder

**Publish to a folder** makes this PC the reference: Foundry OSD writes its configuration to a folder, and other PCs that [follow the folder](follow-a-folder.md) apply every new version by themselves. Only the reference PC changes the configuration; on the followers it is read-only.

## Before you start

- **A folder.** A network share, a folder synchronized by OneDrive or SharePoint, or a local folder. It must be empty, or already hold a publication of this PC. The reference PC needs to write in it, the followers to read it.
- **A publication password.** At least 12 characters, and not the same character repeated. Followers need it to read the folder.
- **A complete configuration.** Foundry OSD publishes only a configuration that is ready to create media, so clear every row marked **Needs attention** on [Start](../media/README.md) first.

## Publish

1. Open **Settings**, expand **Configuration sharing**, and select **Publish** on **Publish to a folder**.
2. In **Folder**, type the path or select **Browse**.
3. Type a **Publication password**, or select **Generate**, and type it again in **Confirm the password**. **Copy** puts it on the clipboard.
4. Decide on **Include passwords**. With it, the dialog warns: "Anyone who can read the folder and knows the publication password can read these passwords." Without it, each follower types the passwords on its own pages.
5. Select **Publish**.

The card shows "This PC publishes its configuration" and the status "Published. Followers receive this version at their next check." Give the folder path and the publication password to the people who set up the followers, the password by another channel.

If another PC already publishes to the folder, the **Take over the folder** dialog opens instead: see [Take over a folder](#take-over-a-folder).

## How changes are published

Foundry OSD publishes by itself about 5 seconds after you change the configuration or an included password. Each publication increases the **Version** on the card.

- A configuration that is not ready to create media, or that uses content missing on this PC, is not published: "Not published: the configuration is not ready to build media or references content missing on this PC. The previous version stays current." Fix it and the next change publishes.
- Post-installation packages, custom driver sets and custom Windows images are copied to the folder in the background, with a progress bar on the card. Followers see the new version only once all its content is written.
- **Retry** publishes again at once, for example after the folder was unavailable.

Do not edit, move or delete files in the folder. It holds `current.json`, which points to the current version, and the `manifests` and `content` folders, encrypted.

## Change password or reset the publication

Both actions ask for a new publication password. Followers then show that the password does not open the publication, and each one selects **Enter password** once.

| Button | What changes | Former password holders |
| --- | --- | --- |
| **Change password** | The current version is rewritten under the new password. Content files are not encrypted again. | Cannot read new versions, but someone who knew the old password can still read the content files. |
| **Reset publication** | A new password and a new content key: every content file is encrypted again. | Completely revoked once the reset is published. |

Until a reset is published, the card shows "Old readers can still open content until the next successful publish." When this PC cannot publish at that moment, the card shows "The new password is saved. The change completes at the next successful publish."

## Take over a folder

Taking over continues the publication of another PC from this PC, for example when the reference PC is replaced. It needs the current publication password.

1. Select **Publish to a folder** and choose the folder, or select **Take over** on the card when it shows "Another PC publishes to this folder. Take it over to publish from this PC."
2. In **Take over the folder**, compare **Version found** and **Key found** with what you expect. They are not verified until the password opens the publication.
3. Type the **Current publication password**, decide on **Include passwords**, and select **Take over**.

Every follower then shows that another PC publishes the folder and must [accept the new reference](follow-a-folder.md#accept-a-new-reference) once. The same dialog recovers this PC when its own publication keys can no longer be read: see [Configuration sharing troubleshooting](../../troubleshooting/foundry-osd/configuration-sharing.md#passwords-needed).

## Stop publishing

**Stop publishing** makes this PC a normal PC. The folder stays as it is, and followers keep the last published version. To publish again later, publish to the same folder with the same publication password.

## Limits

- One reference PC per folder. Another PC publishes there only by taking it over.
- The publication password gives read access; only the signature of the reference PC lets a version be applied. Anyone who can write in the folder can still delete its files. See [Security and credentials](../../reference/security-and-credentials.md#configuration-sharing).
- Foundry OSD keeps the current and the previous version in the folder, and removes the content that neither of them uses.

## Related

- [Follow a folder](follow-a-folder.md)
- [Configuration sharing](../configuration-sharing.md)
- [Configuration sharing troubleshooting](../../troubleshooting/foundry-osd/configuration-sharing.md)
