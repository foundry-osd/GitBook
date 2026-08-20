# Download Foundry OSD

Download the latest Foundry OSD installer that matches the architecture of the Windows administrator workstation.

## Choose an installer

| Architecture | Use this installer for | Download |
| --- | --- | --- |
| x64 | Most Windows workstations with Intel or AMD processors | [Download the latest x64 MSI](https://github.com/foundry-osd/foundry/releases/latest/download/Foundry-win-x64.msi) |
| ARM64 | Windows on Arm workstations | [Download the latest ARM64 MSI](https://github.com/foundry-osd/foundry/releases/latest/download/Foundry-win-arm64.msi) |

{% hint style="info" %}
Foundry Connect and Foundry Deploy do not require separate installation. Foundry OSD provisions the matching runtime components into deployment media during media creation.
{% endhint %}

## Install Foundry OSD

1. Download the MSI for the workstation architecture.
2. Run the downloaded installer.
3. Approve elevation when Windows requests administrator permission.
4. Open Foundry OSD after installation completes.
5. Continue with [Requirements](requirements.md) and the [Quick start](quick-start.md).

## Releases and verification

Use [Foundry releases](https://github.com/foundry-osd/foundry/releases) to review release notes, asset digests, and previous versions.

{% hint style="warning" %}
Download Foundry OSD only from the official `foundry-osd/foundry` GitHub repository. Verify the release and installer architecture before deployment use.
{% endhint %}
