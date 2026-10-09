# Export and import

**Export** writes the configuration in use to one encrypted file, as a backup or to take to another PC. **Import** adds the configuration of such a file as an independent copy. Both are in the [Settings backup and sync](../deployment-profiles.md) card of **Settings**.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-deployment-profiles-export-01-import-preview.png`
- **Capture:** Show the whole **Import** preview dialog for a demonstration file, with its counts, the line about missing files and **Remember passwords**.
{% endhint %}

## Export an encrypted file

1. Select the configuration to export in the list of the card.
2. Expand the card and select **Export**.
3. Choose where to save the file. The suggested name is `Foundry-profile.foundryprofile`.
4. Type a **File password** and repeat it in **Confirm file password**. Any password of 1 to 1,024 characters is accepted: choose a long one.
5. Decide on **Include passwords and confidential files**, then select **Continue**.

| The file contains | Without the option | With the option |
| --- | --- | --- |
| Every option of the configuration, including Windows Autopilot JSON profiles | Yes | Yes |
| Passwords: Wi-Fi, Deployment password, local accounts of OOBE, certificates | No | Yes |
| Confidential files present on this PC: answer files, Ethernet 802.1X and Wi-Fi profiles, PFX certificates | No | Yes |
| Passwords of the join accounts of zero-touch Domain Join | No | No |
| Custom Windows images, post-installation packages, the custom driver folder | No | No |

Anyone who has the file and its password can read what it contains, certificate private keys included. Send the password by another channel than the file.

## Preview and import

1. Expand the card and select **Import**, then choose the `.foundryprofile` file.
2. Type its **File password** and select **Continue**.
3. Read the preview. It gives the configuration name, **Files included** and **Passwords and access keys included**. When files are absent it adds "\<number\> files are missing or were not included. Select their locations before creating media."
4. Check **Remember passwords**. It is selected when the file is complete. When a password or a file is missing, leave it cleared: with it selected, the import stops with "Some passwords or source files are missing. Add them before saving or sharing passwords."
5. Select **Continue**.

The imported configuration is added to the list and becomes the one in use. The preview says "This will replace the settings you are using.": it replaces what the pages show, and the configuration you were using is saved first and stays in the list.

## Complete the configuration on this PC

- Select again each file reported as missing, and type the passwords that were not in the file.
- Turn on **Remember passwords** once nothing is missing.
- Set the **Custom driver folder** in [General](../general.md): it is not part of the file. The ISO output path and the telemetry choices of this PC are kept.
- Import the same custom Windows images and post-installation packages on this PC when the configuration uses them.
- Open [Start](../media/README.md) and clear every row marked **Needs attention**.

## Limits

| Limit | Value |
| --- | --- |
| Confidential files in one configuration | 64 |
| Size of one confidential file | 4 MiB |
| Size of all confidential files together | 8 MiB |
| Size of the whole exported file | 16 MiB |

The file limits apply to every save, not only to an export. Beyond them, saving and synchronization also stop with "Couldn't complete this action. Check the file, its password, your access permissions, and your network connection."

- A wrong password or a damaged file gives that same message. The configuration in use is not changed.
- A file written by a newer Foundry OSD is refused with "Foundry can't open this settings format. Update Foundry and try again." A file from an older version is accepted.
- To follow a shared configuration, do not import its connection file. Use **Connect to a shared configuration**, described in [Settings backup and sync](../deployment-profiles.md).

## Related

- [Settings backup and sync](../deployment-profiles.md)
- [Sync and conflicts](sync-and-conflicts.md)
- [Foundry OSD application troubleshooting](../../troubleshooting/foundry-osd.md)
