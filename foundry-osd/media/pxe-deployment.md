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
