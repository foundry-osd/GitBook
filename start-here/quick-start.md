# Quick start

This is the shortest path from an empty workstation to one deployed test device. It uses the default settings; optional features are listed in step 4 and can wait.

## Before you start

- A workstation and a test device that meet the [Requirements](requirements.md).
- A USB drive of at least 16 GB, or a virtual machine that can start from an ISO file.
- A test device whose disk can be erased, connected to a network with Internet access. Ethernet is the simplest choice.

## 1. Install and open Foundry OSD

[Download and install](download.md) Foundry OSD, then start it. Approve the Windows elevation (UAC) prompt, which appears at every start.

## 2. Install the ADK components

Until this step succeeds, every page except **Home**, **ADK** and **Settings** is disabled.

1. Open **ADK**.
2. Select **Install Windows ADK and Windows PE Add-on**.
3. Wait for the status **ADK is ready**. The download and installation can take several minutes and cannot be cancelled.

If the page shows another status or another button, see [ADK](../foundry-osd/adk.md).

## 3. Check General

Open **General**. Foundry OSD selects the **WinPE boot language** when this page opens, and media creation stays blocked until one is selected, so open the page at least once.

For a first test, turn off **Automatic restart** or raise **Restart delay**. By default the device restarts 10 seconds after a successful deployment, which leaves little time to read the result.

The other options are explained in [General](../foundry-osd/general.md).

## 4. Add optional features

Skip this step for a first test on Ethernet. Each feature is independent:

- [Network](../foundry-osd/network/README.md): Wi-Fi or Ethernet 802.1X in Windows PE.
- [Windows Autopilot](../foundry-osd/autopilot/README.md) or [Domain Join](../foundry-osd/domain-join/README.md): only one of the two can be active.
- [Customization](../foundry-osd/customization/README.md): Windows releases offered, computer name, OOBE, post-installation actions and more.
- [Password protection](../foundry-osd/general.md#password-protection): a password the technician must type before deploying.

## 5. Create the media

1. Open **Start**.
2. Fix every item marked **Needs attention**.
3. Select **Create ISO**, or connect the USB drive, select it and select **Create USB**.

{% hint style="danger" %}
**Create USB** erases the selected drive. Check the disk name and size in the confirmation before you continue.
{% endhint %}

Details: [Create an ISO](../foundry-osd/media/create-iso.md), [Create a USB drive](../foundry-osd/media/create-usb.md).

## 6. Start the test device

Start the device from the media in one of three ways:

- From the USB drive, through the firmware boot menu.
- From the ISO file, attached to a virtual machine or a remote management console.
- From the network, when you publish the media with [PXE](../foundry-osd/media/pxe-deployment.md).

A console window shows the [Windows PE startup](../foundry-connect/windows-pe-startup.md), then Foundry Connect opens.

## 7. Deploy Windows

1. In [Foundry Connect](../foundry-connect/README.md), wait for the network check. Once Internet access is confirmed, Foundry Connect continues by itself after 10 seconds; **Continue** skips the wait.
2. In [Foundry Deploy](../foundry-deploy/README.md), select **Start deployment**.
3. **Target device**: [select the disk](../foundry-deploy/target.md). This disk is erased.
4. **Operating system**: [select Windows](../foundry-deploy/operating-system.md), with its release, language, edition and licensing.
5. **Drivers**: [select a driver pack](../foundry-deploy/driver-pack.md), or keep the proposed source.
6. **Summary**: [review and deploy](../foundry-deploy/review-and-deploy.md). Check the summary, start, and confirm.
7. Read the result on the final page, as described in [Verify deployment](../foundry-deploy/verify-deployment.md).

## 8. Let Windows finish

After the restart, Windows completes its setup and Foundry runs its last tasks before the first sign-in. [After the restart](../foundry-deploy/after-the-restart.md) describes what you see.

## If something goes wrong

Start from [Troubleshooting](../troubleshooting/README.md): it is organized by the stage where the problem appears.
