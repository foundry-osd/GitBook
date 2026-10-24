# Publish to a folder

**Publish to a folder** makes this PC the reference: Foundry OSD writes its configuration to a folder, and the PCs that [follow the folder](follow-a-folder.md) apply each new version, read-only.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-configuration-sharing-publish-01-dialog.png`
- **Capture:** Show the **Publish to a folder** dialog filled in: a folder, a generated publication password with its confirmation, and **Include passwords** checked with its warning.
{% endhint %}

## Before you start

- **A folder**: a network share, a OneDrive or SharePoint folder, or a local folder, empty or already published by this PC. This PC writes in it; followers read it.
- **A publication password**, which followers need.
- **A configuration ready to create media**: no row marked **Needs attention** on [Start](../media/README.md).

## Publish

1. Open **Settings**, expand **Configuration sharing**, and select **Publish** on **Publish to a folder**.
2. In **Folder**, type the path or select **Browse**.
3. Type a **Publication password**, or select **Generate**, and type it again in **Confirm the password**. **Copy** puts it on the clipboard.
4. Decide on **Include passwords**. With it, the dialog warns: "Anyone who can read the folder and knows the publication password can read these passwords."
5. Select **Publish**. The dialog shows a progress bar with a percentage while the content is copied.

The card shows "This PC publishes its configuration" and the status "Published. Followers receive this version at their next check." Send the publication password to the followers by another channel than the folder path.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-configuration-sharing-publish-02-card-reference.png`
- **Capture:** Show the **Configuration sharing** card of a reference PC: the role, the **Published** status, **Folder**, **Version**, **Last success**, and the buttons **Retry**, **Change password**, **Reset publication** and **Stop publishing**.
{% endhint %}

If another PC already publishes to the folder, the **Take over the folder** dialog opens instead: see [Take over a folder](#take-over-a-folder).

## How changes are published

Foundry OSD publishes by itself about 5 seconds after you change the configuration or an included password. Each publication increases the **Version** on the card. Do not edit, move or delete files in the folder.

- A configuration that is not ready, or uses content missing on this PC, is not published: the status reads "Not published: ..." and the previous version stays current.
- Packages, driver sets and images are copied in the background, with a progress bar on the card. Followers see the version once all its content is written.
- **Retry** publishes at once.

## Change password or reset the publication

Both actions ask for a new password. Each follower then selects **Enter password** once.

| Button | What changes | When followers ask for the new password |
| --- | --- | --- |
| **Change password** | Rewrites the current version under the new password. Content files are not encrypted again: former readers are not revoked. | At the next published version. Until then they stay **Up to date**. |
| **Reset publication** | New password and content key; every content file is encrypted again. The complete revocation. | At once: the reset publishes a new version. |

What a former password holder can still read is in [Revoke read access](../../reference/security-and-credentials.md#revoke-read-access). Until a reset is published, the card shows "Old readers can still open content until the next successful publish." When this PC cannot publish at that moment, the card shows "The new password is saved. The change completes at the next successful publish."

## Take over a folder

Taking over continues the publication of another PC from this one, for example when the reference PC is replaced.

1. Select **Publish** on **Publish to a folder** and choose the folder, or select **Take over** on the card when it shows "Another PC publishes to this folder. Take it over to publish from this PC."
2. In **Take over the folder**, check **Version found** and **Key found**, which are not verified yet.
3. Type the **Current publication password**, decide on **Include passwords**, and select **Take over**. The dialog shows the progress with a percentage.

Each follower must then [accept the new reference](follow-a-folder.md#accept-a-new-reference) once. The same dialog recovers this PC when its own publication keys can no longer be read: see [Configuration sharing troubleshooting](../../troubleshooting/foundry-osd/configuration-sharing.md#passwords-needed).

## Stop publishing

**Stop publishing** makes this PC a normal PC. The folder stays as it is, and followers keep the last version. To publish again, use the same folder and publication password.

## Limits

- One reference PC per folder. Another PC publishes there only by taking it over.
- Foundry OSD keeps the current and the previous version in the folder, and removes the content that neither of them uses.
- What the password and the signature protect is in [Security and credentials](../../reference/security-and-credentials.md#configuration-sharing).

## Related

- [Follow a folder](follow-a-folder.md)
- [Configuration sharing](../configuration-sharing.md)
- [Configuration sharing troubleshooting](../../troubleshooting/foundry-osd/configuration-sharing.md)
