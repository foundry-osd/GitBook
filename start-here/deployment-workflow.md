# Deployment workflow

A Foundry deployment has six phases. The administrator does the first one on a workstation; the other five happen on the target device, where a technician makes a few choices and Foundry does the rest.

<figure>
  <img
    src="../.gitbook/assets/shared-deployment-workflow.svg"
    alt="Six phases of a Foundry deployment: prepare the media in Foundry OSD, Windows PE startup, Foundry Connect, choices and installation in Foundry Deploy, and setup after the restart"
  >
  <figcaption>
    Phase 1 runs on the administrator workstation. Phases 2 to 5 run on the target device in Windows PE, and phase 6 in the installed Windows.
  </figcaption>
</figure>

| Phase | Application | Who acts |
| --- | --- | --- |
| 1. Prepare the media | Foundry OSD | Administrator |
| 2. Start Windows PE | Windows PE startup | Nobody |
| 3. Connect to the network | Foundry Connect | Technician, only when the network needs input |
| 4. Choose what to deploy | Foundry Deploy | Technician |
| 5. Install Windows | Foundry Deploy | Nobody |
| 6. Finish after the restart | Windows and Foundry's post-installation step | Nobody, except for the features noted below |

For the same path as a list of actions, follow the [Quick start](quick-start.md).

## 1. Prepare the media

In [Foundry OSD](../foundry-osd/README.md), the administrator installs the Windows ADK components, sets the options of the deployment, and creates an ISO file or a USB drive. An existing USB drive can also be updated.

The settings are written to the media when it is created. To change them later, create the media again or update the USB drive.

## 2. Start Windows PE

The technician starts the target device from the media. Windows PE loads, and a console window shows Foundry preparing the session: it sets up the environment, gets Foundry Connect and Foundry Deploy ready, and starts them in that order.

Nothing has to be typed here. [Windows PE startup](../foundry-connect/windows-pe-startup.md) explains each line of the console.

## 3. Connect to the network

[Foundry Connect](../foundry-connect/README.md) checks Ethernet, and Wi-Fi when the media includes it, until the device has Internet access. It then continues by itself after 10 seconds.

The technician acts only when the network needs it, for example to choose a Wi-Fi network. Closing Foundry Connect stops the startup: Foundry Deploy does not open.

## 4. Choose what to deploy

[Foundry Deploy](../foundry-deploy/README.md) opens a wizard. Its steps are **Target device**, **Operating system**, **Drivers** and **Summary**. An **Autopilot** or a **Domain join** step can appear before **Summary** when the media uses that feature.

When the administrator turned on [Password protection](../foundry-osd/general.md#password-protection), Foundry Deploy asks for the Deployment password first.

## 5. Install Windows

After the technician confirms, Foundry Deploy erases the selected disk, downloads and applies Windows, adds drivers, and prepares what must run at the first start. A list shows each step and its result. The steps and the conditions of each one are in [Review and deploy](../foundry-deploy/review-and-deploy.md#deployment-steps).

By default the device restarts 10 seconds after a successful deployment. The administrator can change this delay or turn the restart off in [General](../foundry-osd/general.md).

## 6. Finish after the restart

The device starts the installed Windows. Before the first sign-in, Foundry's post-installation step runs its built-in tasks, then the administrator's [post-installation](../foundry-osd/customization/post-installation.md) actions, and performs the Domain Join when one is configured. The interactive Windows Autopilot upload, when it is used, asks the technician to sign in during this phase.

A successful result in phase 5 means that Windows is installed and that this work is prepared, not that it has run. [After the restart](../foundry-deploy/after-the-restart.md) describes what appears on screen and how to check the outcome.
