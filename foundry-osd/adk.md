# Windows ADK and Windows PE

Foundry OSD uses Windows ADK and Windows PE Add-on version `10.1.26100.2454` for its built-in installation workflow.

## What Foundry checks

The ADK page detects:

- Whether the Windows ADK is installed.
- Whether the Windows PE Add-on is installed.
- The installed component versions.
- Whether the detected versions satisfy the required Windows ADK 24H2 (`10.1.26100`) compatibility policy.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-adk-01-status-missing.png`
- **Capture:** Show the ADK page when the required ADK or Windows PE Add-on is missing.
{% endhint %}

## Install missing components

1. Open **ADK** in Foundry OSD.
2. Review the detected status and version information.
3. Select the ADK setup action.
4. Approve elevation when Windows requests administrator permission.
5. Keep Foundry OSD open while the installer is downloaded and executed.
6. Wait for Foundry to refresh the component status.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-adk-02-install-button.png`
- **Capture:** Show the automatic ADK and Windows PE Add-on installation action before installation.
{% endhint %}

{% hint style="info" %}
The automatic installation downloads and installs Windows ADK `10.1.26100.2454` first, followed by Windows PE Add-on `10.1.26100.2454`. Media creation still checks the installed components against the required Windows ADK 24H2 (`10.1.26100`) policy before enabling WinPE workflows.
{% endhint %}

## When the page reports ready

Continue to [general configuration](general.md). WinPE language and media creation options remain blocked until the required components are ready.

## Installation does not complete

- Confirm that Foundry OSD is running with administrator permissions.
- Confirm Internet access and proxy policy.
- Close another ADK installer that may already be running.
- Retry the action after Windows Installer completes any pending operation.
- Review [media creation troubleshooting](../troubleshooting/media-creation.md) if detection remains incorrect after installation.
