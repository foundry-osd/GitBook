# Deployment workflow

Foundry separates authoring from runtime deployment.

## Phase 1: Author deployment media

Use **Foundry OSD** on an administrator workstation to:

- Install or validate Windows ADK and Windows PE components.
- Configure networking and deployment behavior.
- Select Windows customization options.
- Choose Windows Autopilot or [Domain Join](../foundry-osd/domain-join/README.md) when required; only one of them can be active.
- Create or update ISO and USB media.

## Phase 2: Establish network readiness

Boot the target device into Windows PE. **Foundry Connect** checks Ethernet and, when enabled, Wi-Fi connectivity. Deployment continues when the configured readiness checks succeed.

The [Windows PE bootstrap](../reference/bootstrap.md) prepares this session and launches Connect and Deploy in order. Closing Connect stops that sequence.

## Phase 3: Select deployment inputs

**Foundry Deploy** guides the technician through:

- Target disk and computer name.
- Windows release, language, edition, and license channel.
- Driver pack selection.
- Firmware and Windows Autopilot options when configured.

[Domain Join](../foundry-deploy/domain-join.md) adds a wizard step before the summary when there is something to enter or choose: the account and password on Interactive media, and the domain or the OU when the media lists several.

## Phase 4: Apply and configure Windows

Foundry prepares the target disk, downloads and applies Windows, stages drivers and configuration, provisions recovery and optional features, and completes the deployment handoff.

## Phase 5: Verify and reboot

Review the completion state, deployment summary, and any reported error. Reboot only after Foundry reports success or after collecting the required troubleshooting evidence.

## Phase 6: Verify installed-Windows work

After the restart, Windows setup runs the [post-installation](../foundry-osd/customization/post-installation.md) actions before the first sign-in. Domain Join runs there, restarts Windows once, then checks that the computer is a member of the domain. A successful deployment in WinPE only means the join was prepared. Check the [outcome](../foundry-deploy/verify-deployment.md#domain-join) before handing over the computer.

<figure>
  <img
    src="../.gitbook/assets/shared-deployment-workflow.svg"
    alt="Foundry deployment workflow from media authoring through network readiness, Windows deployment, verification, and reboot"
  >
  <figcaption>
    Foundry OSD authors deployment media, Foundry Connect establishes network readiness, and Foundry Deploy applies Windows. The post-installation actions, including Domain Join, run after the restart.
  </figcaption>
</figure>

## Use a custom Windows image

Before creating media, optionally [import and include custom Windows images](../foundry-osd/customization/custom-windows-images.md). Boot the complete ISO or USB, complete Foundry Connect, and select the image and exact index in Deploy. Existing network prerequisites and enabled customizations continue to apply.
