# Download and install

Foundry OSD is installed on the administrator workstation from an MSI package. Foundry Connect and Foundry Deploy need no installation: Foundry OSD puts them on the deployment media it creates.

## Choose an installer

| Workstation processor | Installer |
| --- | --- |
| x64 (Intel or AMD) | [Foundry-win-x64.msi](https://github.com/foundry-osd/foundry/releases/latest/download/Foundry-win-x64.msi) |
| ARM64 (Windows on Arm) | [Foundry-win-arm64.msi](https://github.com/foundry-osd/foundry/releases/latest/download/Foundry-win-arm64.msi) |

Both links always point to the latest release. Release notes, file digests and earlier versions are on the [Foundry releases](https://github.com/foundry-osd/foundry/releases) page.

{% hint style="warning" %}
Download Foundry OSD only from the official `foundry-osd/foundry` GitHub repository.
{% endhint %}

## Install Foundry OSD

1. Check the [Requirements](requirements.md).
2. Run the MSI that matches the workstation processor. It installs Foundry OSD for all users of the workstation.
3. Approve the Windows elevation (UAC) prompt.
4. Foundry OSD needs three Microsoft runtimes. The installer is built to download and install the ones that are missing, so keep the workstation online during setup:
   - .NET 10 Desktop Runtime
   - Microsoft Edge WebView2 Runtime
   - Microsoft Visual C++ Redistributable 14.4
5. Start Foundry OSD from the Start menu and continue with the [Quick start](quick-start.md).

## After installation

- Every start shows a UAC prompt. Foundry OSD always runs with administrator rights, so there is nothing to configure and no need to use "Run as administrator".
- You install the MSI once. Foundry OSD then updates itself, as described in [Settings](../foundry-osd/settings.md#update-app).
- The Windows ADK is installed from inside the app, on the [ADK](../foundry-osd/adk.md) page.

## Uninstall

Remove Foundry OSD from the installed apps list in Windows Settings. Your data stays in two folders, which you can delete when you no longer need it:

| Folder | Contents |
| --- | --- |
| `%ProgramData%\Foundry` | Application settings, logs, downloaded files, working folders and the default ISO output |
| `%LocalAppData%\Foundry` | Your saved configurations, imported custom Windows images and post-installation packages |

If Foundry OSD does not install or start, see [Foundry OSD application troubleshooting](../troubleshooting/foundry-osd.md).
