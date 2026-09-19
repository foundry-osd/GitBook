# Windows ADK and Windows PE

Foundry OSD requires the Windows ADK `10.1.26100` release, revision `2454` or later, with a matching Windows PE Add-on and the required files for the selected architecture to build deployment media. ADK servicing is checked separately and reported as an advisory warning when it cannot be verified.

## What Foundry checks

The ADK page detects:

- Whether the Windows ADK is installed.
- Whether the Windows PE Add-on is installed.
- The installed ADK component version.
- Whether the ADK meets the supported release and minimum revision.
- Whether the recommended Deployment Tools servicing updates can be verified.
- Whether the installed Windows PE components match the ADK release and the image, optional-component and boot files are available for the selected architecture.

<figure>
  <img src="../.gitbook/assets/foundry-osd-adk-01-status-missing.png" alt="Foundry OSD showing missing Windows ADK and Windows PE Add-on components">
  <figcaption>Foundry identifies each required component that is not ready.</figcaption>
</figure>

## Install missing components

1. Open **ADK** in Foundry OSD.
2. Review the detected status and version information.
3. Select the ADK setup action.
4. Approve elevation when Windows requests administrator permission.
5. Keep Foundry OSD open while the installer is downloaded and executed.
6. Wait for Foundry to refresh the component status.

<figure>
  <img src="../.gitbook/assets/foundry-osd-adk-02-install-button.png" alt="Foundry OSD automatic Windows ADK and Windows PE Add-on installation action">
  <figcaption>Install the supported ADK and Windows PE Add-on directly from Foundry OSD.</figcaption>
</figure>

{% hint style="info" %}
The automatic installation downloads and installs Windows ADK `10.1.26100.2454` first, followed by Windows PE Add-on `10.1.26100.2454`. Apply the ADK servicing update afterward if the page still reports that servicing needs attention.
{% endhint %}

## Verify ADK servicing

Foundry checks the DISM, Windows System Image Manager and Oscdimg updates supplied in `KB5101684`. An unchanged ADK version number does not tell you whether these updates are installed.

1. Select **ADK update instructions** on the ADK page.
2. Follow [Microsoft's ADK patch instructions](https://learn.microsoft.com/windows-hardware/get-started/adk-servicing) for the `10.1.26100.2454` release and apply all applicable patches with administrator permissions.
3. Return to Foundry and select **Refresh**.

If the update is not verified or its status cannot be read, Foundry displays a warning. You can continue configuring and creating ISO or USB media when the ADK version and selected architecture meet the prerequisites. Applying the applicable Microsoft security updates remains recommended. If you have already applied a newer update and the status remains unchanged, collect the [logs and support details](../troubleshooting/logs-and-support.md) with the installed update name. An unverified result can mean that the installed update is not recognized. If servicing information cannot be read, check permissions and the installation, then refresh the status.

ADK servicing checks do not verify cumulative updates inside your Windows PE image. Follow Microsoft's guidance separately when servicing that image.

## Repair Windows PE readiness

The readiness details show whether the WinPE files are available for **x64** and **ARM64**. The separate media creation capability indicates whether the ADK and WinPE prerequisites are met. Missing files for one architecture block that target; a complete other architecture remains available.

- If the matching add-on is already installed but files are missing, use its installer to **Repair** it.
- If its version differs from the ADK, uninstall the Windows PE Add-on first, then install the matching `10.1.26100.2454` add-on from [Microsoft's ADK downloads](https://learn.microsoft.com/windows-hardware/get-started/adk-install).
- Return to Foundry and select **Refresh**. Recheck ADK servicing after a repair or reinstall changes Deployment Tools files; apply the applicable updates again if required.

Foundry's ordinary install action is for missing components. Repair or replacement of a registered add-on is performed through its installer.

## When the page reports ready

Continue to [general configuration](general.md). WinPE language and media creation options remain blocked until the required components are ready.

## Installation does not complete

- Confirm that Foundry OSD is running with administrator permissions.
- Confirm Internet access and proxy policy.
- Close another ADK installer that may already be running.
- Retry the action after Windows Installer completes any pending operation.
- Review [media creation troubleshooting](../troubleshooting/media-creation.md) if detection remains incorrect after installation.
