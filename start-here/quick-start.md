# Quick start

This path creates deployment media with the minimum required configuration and deploys one test device.

## 1. Open Foundry OSD as an administrator

Administrator permissions are required to install deployment components and create boot media.

## 2. Prepare Windows ADK and Windows PE

Open **ADK** in Foundry OSD. Review the detected status and use the installation button if a required component is missing or reported as incompatible. The built-in workflow installs Windows ADK and Windows PE Add-on `10.1.26100.2454`.

See [Windows ADK and Windows PE](../foundry-osd/adk.md).

## 3. Review general configuration

Open **General configuration** and confirm the deployment settings. Configure protected deployment only if technicians must unlock the media with a password.

## 4. Configure only what you need

- Add Wi-Fi or Ethernet 802.1X settings when the target cannot use an open wired network.
- Choose Windows customization options required by the organization.
- Configure Windows Autopilot only when the device must be registered or receive a profile.

## 5. Create deployment media

Open **Start**, resolve every blocking readiness item, and create an ISO or USB drive.

{% hint style="danger" %}
USB creation can erase the selected drive. Verify the device identity and capacity before confirming.
{% endhint %}

## 6. Boot the target device

Boot the device from the ISO or USB media. Foundry Connect verifies network readiness before Foundry Deploy begins.

## 7. Deploy Windows

In Foundry Deploy:

1. Select the target disk.
2. Select a Windows release, language, edition, and license channel.
3. Select a compatible driver pack.
4. Review the summary and start deployment.
5. Verify the success page before rebooting.
