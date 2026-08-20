# Foundry OSD documentation

Foundry OSD helps Windows deployment teams prepare boot media, connect devices in Windows PE, and deploy Windows through a guided workflow.

## Choose your task

- [Prepare Foundry OSD](start-here/quick-start.md) to configure an administrator workstation and create your first deployment media.
- [Configure deployment media](foundry-osd/README.md) to define networking, Windows customization, and Windows Autopilot behavior.
- [Connect a device](foundry-connect/README.md) after booting into Windows PE.
- [Deploy Windows](foundry-deploy/README.md) by selecting a target disk, operating system, and driver pack.
- [Troubleshoot a problem](troubleshooting/README.md) using stage-specific symptoms, evidence, and resolutions.

## How Foundry works

Foundry uses three applications in one deployment lifecycle:

1. **Foundry OSD** runs on an administrator workstation and creates ISO or USB deployment media.
2. **Foundry Connect** runs in Windows PE and verifies that the target device has usable network access.
3. **Foundry Deploy** runs in Windows PE and guides the technician through Windows deployment.

{% hint style="warning" %}
Creating or updating USB media and deploying Windows can erase data. Verify the selected USB device and target disk before confirming a destructive operation.
{% endhint %}

Continue with the [quick start](start-here/quick-start.md) or review the complete [deployment workflow](start-here/deployment-workflow.md).
