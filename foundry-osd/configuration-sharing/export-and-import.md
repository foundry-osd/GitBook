# Export and import

**Export configuration** writes your configuration to one encrypted `.foundryconfig` file, as a backup or to take to another PC. **Import configuration** replaces the configuration of a PC with the content of such a file. Both are on the [Configuration sharing](../configuration-sharing.md) card of **Settings**.

What the file carries, and what it leaves out, is listed in [What travels](../configuration-sharing.md#what-travels).

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-configuration-sharing-export-01-export-dialog.png`
- **Capture:** Show the **Export configuration** dialog with a generated passphrase and its confirmation, and **Include passwords** checked with its warning.
{% endhint %}

## Export a configuration

1. Open **Settings**, expand **Configuration sharing**, and select **Export** on **Export configuration**.
2. Type a **Passphrase**, or select **Generate** to get 20 random characters in groups of four, such as `xxxx-xxxx-xxxx-xxxx-xxxx`. **Copy** puts it on the clipboard.
3. Type it again in **Confirm passphrase**. **Export** stays unavailable until the passphrase follows the rule shown under it: "At least 12 characters, and not the same character repeated (for example aaaaaaaaaaaa)."
4. Decide on **Include passwords**. With it, the dialog warns: "Anyone who has this file and its passphrase can read these passwords."
5. Select **Export** and choose where to save the file. The suggested name is `Foundry-configuration.foundryconfig`.

The card then shows "Configuration exported." Send the passphrase by another channel than the file.

When a selected file is missing or damaged on this PC, the file is still written without it, and the dialog stays open with "The configuration was exported, but some files are missing or damaged on this PC and were not included (count: \<number\>)." Select those files again on their pages, then export again.

## Import a configuration

1. Expand **Configuration sharing** and select **Import** on **Import configuration**.
2. Under **File**, select **Browse** and choose the `.foundryconfig` file.
3. Type its **Passphrase** and select **Open**.
4. Read the preview: **Name**, **Exported on**, and **Passwords**, which reads **Included** or **Not included**.
5. Read **Missing on this PC**, when it appears. It lists each post-installation package, driver set, image or file that the configuration uses and this PC does not have, with its kind.
6. Read the warning "Importing replaces the current configuration. The ISO output path, the WinPE driver folder and the telemetry settings of this PC are kept.", then select **Import**.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-configuration-sharing-export-02-import-preview.png`
- **Capture:** Show the **Import configuration** dialog after **Open**: the preview with **Name**, **Exported on** and **Passwords**, and **Missing on this PC** listing a driver set, a post-installation package and an image.
{% endhint %}

The card then reports one of these results:

| Message | Meaning |
| --- | --- |
| "Configuration imported." | Everything the configuration uses is on this PC. |
| "Configuration imported. Some content is missing on this PC: import it again on its page." | The items listed under **Missing on this PC** are still missing. |
| "Configuration imported, but its passwords could not be saved. Enter them again on the configuration pages." | The options and files are imported; type the passwords on their pages. |

When profiles of Settings backup and sync remain on this PC, the dialog also lists them under **Profiles from the previous version**: see [Move from Settings backup and sync](migration.md#other-profiles-of-this-pc). The dialog imports one source at a time: opening a file clears the profile selection, and opening a profile clears the file and its passphrase.

## Complete the configuration on this PC

- Import each missing post-installation package, driver set and image on its page: [Post-installation](../customization/post-installation.md), [Custom drivers](../customization/custom-drivers.md), [Custom Windows images](../customization/custom-windows-images.md). Until then, these items show **Needs attention** on **Start**.
- Without the passwords in the file, type them on their pages. Foundry OSD then [saves them](saved-passwords.md) for your Windows account.
- Open [Start](../media/README.md) and clear every row marked **Needs attention**.

## Limits

- Import is refused on a PC that follows a folder: "Import is not available while this PC follows a folder. Stop following first."
- On a PC that publishes to a folder, an import is a change like any other and is published.
- The files of the configuration must not exceed 16 MiB together. Otherwise the export stops with "The files of this configuration exceed 16 MiB in total and cannot be exported."
- A passphrase holds 12 to 1,024 characters.
- A wrong passphrase or a damaged file gives "Wrong passphrase or damaged file." and the configuration of this PC is not changed. Other messages are in [Configuration sharing troubleshooting](../../troubleshooting/foundry-osd/configuration-sharing.md#export-and-import-messages).

## Related

- [Configuration sharing](../configuration-sharing.md)
- [Security and credentials](../../reference/security-and-credentials.md#configuration-sharing)
