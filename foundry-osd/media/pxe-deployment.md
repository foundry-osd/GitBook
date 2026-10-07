# Deploy with PXE

PXE is not officially supported as a Foundry OSD media output. Foundry OSD does not configure or manage PXE, DHCP, TFTP, or third-party PXE server infrastructure.

{% hint style="warning" %}
Use this workaround only with an existing, working PXE environment. PXE configuration, operation, and support remain the responsibility of the PXE infrastructure owner.
{% endhint %}

## Before you begin

Confirm that you have:

- An existing PXE environment that supports importing and starting WIM boot images.
- Permission to import, configure, and advertise boot images on the PXE server.
- A client architecture and firmware mode compatible with the Foundry OSD ISO and PXE environment.
- Any WinPE network drivers required by the target hardware included when the ISO is created.
- Network access from Windows PE to the services required by [Foundry Connect](../../foundry-connect/README.md) and [Foundry Deploy](../../foundry-deploy/README.md).

## Prepare the boot image

1. [Create an ISO](create-iso.md) and validate that it boots on representative hardware or a virtual machine.
2. Mount the validated ISO.
3. Locate `sources\boot.wim` on the mounted ISO.
4. Copy `sources\boot.wim` to a location accessible to the PXE administrator.
5. Import the copied WIM as a boot image into the existing PXE server.
6. Configure and advertise the boot image according to the PXE vendor documentation.

## Validate the deployment

1. Boot a representative client from the imported image.
2. Confirm that Windows PE obtains the required network access.
3. Confirm that [Foundry Connect](../../foundry-connect/README.md) starts and reports network readiness.
4. Continue and confirm that [Foundry Deploy](../../foundry-deploy/README.md) starts.
5. Complete one representative end-to-end deployment.
6. Confirm that Foundry Deploy reports successful completion.
7. Complete the relevant [post-boot checks](../../foundry-deploy/verify-deployment.md).

Resolve driver, architecture, firmware, or network compatibility issues in the ISO and PXE environment before wider deployment.

## Maintain the boot image

Re-import `sources\boot.wim` whenever the Foundry OSD ISO is regenerated. Keep the previous boot image available until the replacement has passed PXE boot and runtime validation on representative clients.

## What the boot image carries

A PXE server delivers only `sources\boot.wim`. Content that Foundry OSD stores elsewhere on the ISO or USB does not reach the target.

| Content | Location | With the boot image alone |
| --- | --- | --- |
| Deployment settings, including network, Autopilot, answer file, naming, OOBE, optional feature, app removal, and AI component choices | Inside `boot.wim` | Delivered |
| [Post-installation](../customization/post-installation.md) built-in tasks, Command line actions without imported content, and Restart Windows actions | Inside `boot.wim` | Delivered |
| Post-installation PowerShell scripts, Software installers, and Command line actions with imported content | Outside `boot.wim` | Not delivered |
| [Custom Windows images](../customization/custom-windows-images.md) | Outside `boot.wim` | Not delivered |

Windows images, driver packs, and the Foundry Deploy and PostInstall applications that are downloaded during deployment still require the network access listed in [Before you begin](#before-you-begin). Validate every enabled customization on a representative client before wider deployment.

## Custom image payloads

[Custom Windows images](../customization/custom-windows-images.md) are external to `sources\boot.wim`. Copying that boot image alone does not transfer custom images or their manifest. This feature does not provide a supported PXE delivery path for custom WIMs; use the complete generated ISO or USB media.

## Post-installation content

Imported [Post-installation](../customization/post-installation.md) content is stored on the ISO or USB, outside `sources\boot.wim`, so a PXE server does not deliver it. This affects every PowerShell script and Software action, and Command line actions that use imported content.

Built-in tasks, Command line actions without imported content, and Restart Windows actions need nothing from the media. They run when only the boot image is available.

To use imported content with a PXE boot, keep the complete generated ISO attached to the target, or provide its matching Foundry USB cache media, until deployment finishes. Do not mix the boot image and content from different media builds. Foundry does not download this content from an HTTP or SMB share. When required content is missing, deployment stops before the target disk is prepared; see [Post-installation preparation fails](../../troubleshooting/deployment.md#post-installation-preparation-fails). This does not provide a supported PXE delivery path.
