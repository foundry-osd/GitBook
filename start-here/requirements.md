# Requirements

Foundry OSD prepares Windows deployment media on an administrator workstation. The target device runs Foundry Connect and Foundry Deploy from Windows PE.

## Administrator workstation

- Windows 10 or Windows 11.
- Local administrator permissions.
- Internet access.
- Sufficient free space for Windows media, drivers, temporary workspace files, and the output ISO or USB content.
- Windows ADK 24H2 on the supported `10.1.26100` build line.
- Windows PE Add-on on the same supported `10.1.26100` build line.

Foundry OSD detects the ADK and Windows PE Add-on. If a required component is missing, use the installation action on the [Windows ADK and Windows PE page](../foundry-osd/adk.md). The current built-in installation workflow downloads version `10.1.26100.2454` for both components.

{% hint style="info" %}
Foundry OSD manages the normal ADK and Windows PE installation workflow. Manual installation is not required for a standard setup.
{% endhint %}

## Deployment media

Choose one output type:

- An ISO file for virtual machines, remote media, or external imaging tools.
- A USB drive for physical deployment. The selected USB drive can be erased during creation.

## Target device

The target device must support the architecture and Windows release selected during media authoring. Wired networking is recommended for large downloads. Wi-Fi can be provisioned when the hardware and Windows PE drivers support it.

## Microsoft cloud services

Windows Autopilot workflows may require:

- A Microsoft Entra tenant.
- Microsoft Intune.
- Microsoft Graph permissions appropriate to the selected Autopilot method.
- An application registration and certificate for zero-touch upload.
- An authorized user for interactive upload.

See [Choose an Autopilot method](../foundry-osd/autopilot/README.md) before configuring cloud credentials.
