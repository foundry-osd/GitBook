# Download and install

Foundry OSD is installed on the administrator PC from an MSI package. Foundry Connect and Foundry Deploy need no installation: the deployment media that Foundry OSD creates starts them on the target device.

## Choose an installer

| PC processor | Installer |
| --- | --- |
| x64 (Intel or AMD) | [Foundry-win-x64.msi](https://github.com/foundry-osd/foundry/releases/latest/download/Foundry-win-x64.msi) |
| ARM64 (Windows on Arm) | [Foundry-win-arm64.msi](https://github.com/foundry-osd/foundry/releases/latest/download/Foundry-win-arm64.msi) |

Both links always point to the latest release. Release notes, file digests and earlier versions are on the [Foundry releases](https://github.com/foundry-osd/foundry/releases) page.

{% hint style="warning" %}
Download Foundry OSD only from the official `foundry-osd/foundry` GitHub repository.
{% endhint %}

## Install Foundry OSD

1. Check that the PC meets the [Requirements](requirements.md), if you have not done so yet.
2. Run the MSI that matches the PC processor. It installs Foundry OSD for all users of the PC. On an ARM64 PC, the x64 version opens only a dialog titled **ARM64 version required** and then closes: see [ARM64 version required](../troubleshooting/foundry-osd.md#arm64-version-required).
3. Approve the Windows elevation (UAC) prompt.
4. Foundry OSD needs three Microsoft runtimes. The installer is built to download and install the ones that are missing, so keep the PC online during setup:
   - .NET 10 Desktop Runtime
   - Microsoft Edge WebView2 Runtime
   - Microsoft Visual C++ Redistributable 14.4
5. Start Foundry OSD from the Start menu and continue with the [Quick start](quick-start.md).

## After installation

- Every start shows a UAC prompt. Foundry OSD always runs with administrator rights, so there is nothing to configure and no need to use "Run as administrator".
- You install the MSI once. Foundry OSD then updates itself, as described in [Settings](../foundry-osd/settings.md#update-app). Deployment media keep the Foundry applications of the version that created them: create them again after an update. See [Which version runs](../foundry-connect/windows-pe-startup.md#which-version-runs).
- The Windows ADK is installed from inside the app, on the [ADK](../foundry-osd/adk.md) page.

## Uninstall

Remove Foundry OSD from the installed apps list in Windows Settings. Your data stays in two folders, which you can delete when you no longer need it:

| Folder | Contents |
| --- | --- |
| `%ProgramData%\Foundry` | Application settings, logs, downloaded files, working folders and the default ISO output |
| `%LocalAppData%\Foundry` | Your configuration with its saved passwords and files, imported custom Windows images, custom driver sets and post-installation packages |

Foundry OSD also keeps entries whose names start with `FoundryOSD/` in Windows Credential Manager, for your Windows account: the proxy credentials, and the entries of Settings backup and sync if you used it. Remove them in **Credential Manager** > **Windows Credentials** if you want nothing left.

If Foundry OSD does not install or start, see [Foundry OSD application troubleshooting](../troubleshooting/foundry-osd.md).
