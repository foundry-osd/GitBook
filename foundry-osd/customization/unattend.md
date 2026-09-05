# Custom answer files (Unattend)

{% hint style="info" %}
Unreleased feature: this page describes [Foundry PR #314](https://github.com/foundry-osd/foundry/pull/314). Custom answer-file import and selection are not available in release `v26.9.1.1`. The application PR remains draft pending manual WinPE and Windows first-boot validation.
{% endhint %}

Embed one or more Windows answer files in the boot image, then let the technician choose one in Foundry Deploy. Each file is an alternative deployment configuration. Foundry does not merge files or combine a custom file with its generated naming and OOBE settings.

## Prepare the files

Use answer files written for the Windows architecture, edition, and version you deploy. Only the `specialize` and `oobeSystem` passes are supported. These can configure computer name, time zone, local accounts, AutoLogon, OOBE, and commands valid in those passes.

Foundry applies the Windows image with DISM. Nonempty `windowsPE`, `offlineServicing`, `generalize`, `auditSystem`, and `auditUser` sections, root-level `servicing` instructions, and explicit audit-mode resealing are rejected. A complete Windows Setup `Autounattend.xml` may need to be adapted before import. Foundry does not silently remove unsupported sections.

Each file must be no larger than 4 MiB. DTDs and external entity resolution are prohibited. Foundry preserves the original XML bytes, encoding, and extension content. It does not convert architecture-specific components or install language resources requested by the file.

Validate the answer file with Windows System Image Manager for the target Windows image, then test a complete deployment in a representative VM. Import validation alone cannot establish that every Windows setting or custom command will work.

## Import and build media

1. Open **Customization > Unattend** in Foundry OSD and enable the feature using the switch in the page header. Its controls remain disabled while the feature is off. The documentation button beside the switch opens this guide.
2. Import one or more XML files. Review the validation results and give each file a recognizable display label.
3. Choose a default file, or keep **Use Foundry settings** as the default.
4. Enable [Protected deployment](../general.md#protected-deployment) and enter the media password. Protection is required for every custom file, even one that appears to contain no credentials.
5. Return to **Start**, resolve readiness errors, and [create deployment media](../media/README.md).

{% hint style="warning" %}
**Screenshot required before publication**

- **File:** `foundry-osd-unattend-01-catalog.png`
- **Capture:** Show the Unattend page with two sanitized sample files, validation results, the header enable switch and documentation button, and the default selection. Exclude source paths containing personal information.
{% endhint %}

Saved Foundry configurations contain source paths and content fingerprints, not the XML. Keep the original files accessible until media creation finishes. Built media contains encrypted copies and no longer needs those sources.

Use **Refresh source** to accept edits to an imported source. Use **Check sources** to recheck availability and validity without accepting changed contents. A missing or changed source blocks media creation until corrected. Duplicate content is kept as one catalog entry.

You can rename display labels, remove files, and change the default. Disabling the feature retains the authoring catalog but excludes its files from newly generated media. Keep native Foundry settings valid because technicians can still select **Use Foundry settings**.

## Select a file during deployment

Unlock the media, then select a file on [Target](../../foundry-deploy/target.md#select-a-custom-answer-file-unreleased), before the computer-name field. Review the active choice and compatibility messages in the deployment summary and confirmation.

Selecting a custom file makes it responsible for naming, time zone, and OOBE. Selecting **Use Foundry settings** restores native behavior. A missing default or an invalid selected file blocks deployment; Foundry never silently substitutes another choice.

Foundry validates and retains the selected file before preparing the target disk. After applying Windows, it writes those exact bytes to `Windows\Panther\unattend.xml` on the target. Automatic discovery at a USB drive's root and runtime file browsing are not included; import files while preparing media.

## Precedence and compatibility

| Configuration area | When a custom file is selected |
| --- | --- |
| Computer name and time zone | The file controls them. Omitted values use Windows or image defaults. |
| Foundry OOBE, privacy policies, local accounts, and Administrator setup | Suppressed, including associated registry writes and runtime account-password processing. |
| Disk layout, Windows selection, recovery, firmware, and offline drivers | Continue to follow Foundry settings. |
| Optional features, AppX and AI removal, deferred drivers, and network roaming | Continue to follow Foundry settings. Custom commands can interfere with their effects. |
| Autopilot JSON profile or interactive registration | Known incompatible account, OOBE, or domain-join settings block deployment. Use a compatible file or change the authored Autopilot configuration. |
| Hardware hash upload from WinPE | Registration can continue. A successful upload does not guarantee enrollment through customized OOBE. |

Custom mode owns the whole naming and OOBE area, including settings omitted from the file. Foundry does not fill gaps with native accounts or privacy settings.

Foundry detects known XML conflicts but cannot predict arbitrary scripts. Arrange access to scripts referenced by the file; importing XML does not bundle those external files or execute its commands in WinPE.

Some Foundry customizations depend on `SetupComplete.cmd`. Custom commands that replace setup hooks, reboot at the wrong time, or change enrollment and package state can disrupt them. Product-key and edition restrictions can also affect setup hooks. For dependent first-logon actions, use a single script that controls sequencing. Test the whole combination before production use.

## Protect sensitive content

The complete custom file is encrypted on deployment media using the existing Protected deployment key. Protection does not extend to the original source file or the decrypted copy Windows needs on the target.

Treat Panther answer files and their copies as sensitive. Windows password hiding is reversible, and custom commands or extensions can contain secrets that Windows will not automatically remove. Do not attach raw XML to support reports.

Do not remove the target answer file before `oobeSystem` has consumed it. Arrange cleanup after the required setup passes through your deployment process. Foundry preserves the file and does not insert a cleanup command. Follow the wider [security and credential guidance](../../reference/security-and-credentials.md).

## Resolve common problems

| Symptom | Action |
| --- | --- |
| Source changed or cannot be read | Restore access, then check sources. If edits were intentional, refresh that file before rebuilding. |
| A network-source check times out | Restore the source location or remove the unavailable entry and recheck. If both background checks remain waiting for file access, restore connectivity and retry. |
| Default file is unavailable | Choose an available default or explicitly select **Use Foundry settings**, then rebuild media if the authored catalog is incorrect. |
| File is incompatible with selected Windows | Use a file with supported components for that architecture and validate it against the target image. |
| Autopilot conflict | Remove the incompatible settings from the source and refresh it, or change Autopilot configuration before rebuilding. |
| Deployment succeeds but Windows setup fails | Inspect Windows setup diagnostics without exposing secrets. Check the file against the selected image and test its commands and setup-hook dependencies. |

After deployment, [verify Windows through first boot and OOBE](../../foundry-deploy/verify-deployment.md). A successful Foundry deployment does not confirm that Windows has consumed every answer-file setting.
