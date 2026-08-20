# Windows ADK and Windows PE

Foundry OSD uses the Windows Assessment and Deployment Kit and the Windows PE Add-on to build boot media.

## What Foundry checks

The ADK page detects:

- Whether the Windows ADK is installed.
- Whether the Windows PE Add-on is installed.
- The installed component versions.
- Whether the detected versions are supported and compatible.

<!-- SCREENSHOT_PENDING: foundry-osd-adk-01-status-missing.png | Show the ADK page when the required ADK or Windows PE Add-on is missing. -->

## Install missing components

1. Open **ADK** in Foundry OSD.
2. Review the detected status and version information.
3. Select the installation action for the missing component.
4. Approve elevation when Windows requests administrator permission.
5. Keep Foundry OSD open while the installer is downloaded and executed.
6. Wait for Foundry to refresh the component status.

<!-- SCREENSHOT_PENDING: foundry-osd-adk-02-install-button.png | Show the automatic ADK and Windows PE Add-on installation action before installation. -->

{% hint style="info" %}
Install the Windows ADK before the Windows PE Add-on. The Add-on must match the supported ADK family.
{% endhint %}

## When the page reports ready

Continue to [general configuration](general.md). WinPE language and media creation options remain blocked until the required components are ready.

## Installation does not complete

- Confirm that Foundry OSD is running with administrator permissions.
- Confirm Internet access and proxy policy.
- Close another ADK installer that may already be running.
- Retry the action after Windows Installer completes any pending operation.
- Review [media creation troubleshooting](../troubleshooting/media-creation.md) if detection remains incorrect after installation.
