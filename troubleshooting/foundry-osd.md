# Foundry OSD application troubleshooting

This page covers the Foundry OSD application itself: opening and closing it, the **ADK** page, the options of **General**, updates, the proxy and the diagnostics export. Three kinds of problems are described elsewhere:

| Where the problem shows | Go to |
| --- | --- |
| A message under the **Settings backup and sync** card of **Settings**, or in one of its dialogs: export, import, sharing, synchronization | [Settings backup and sync](foundry-osd/settings-backup-and-sync.md) |
| A message on a Customization page | The "If something goes wrong" section of that page: [Custom Windows images](../foundry-osd/customization/custom-windows-images.md#if-something-goes-wrong), [Unattend](../foundry-osd/customization/unattend.md#if-something-goes-wrong), [Machine naming](../foundry-osd/customization/machine-naming.md#if-something-goes-wrong), [Post-installation](../foundry-osd/customization/post-installation.md#if-something-goes-wrong) |
| **Start**, while media is being created | [Media creation](media-creation.md) |

Otherwise, find what you see below.

## On this page <a href="#on-this-page" id="on-this-page"></a>

| What you see | Go to section |
| --- | --- |
| The window never appears, or closes at once | [Foundry OSD does not open](#does-not-open) |
| Pages grayed out, badge **ADK not ready** | [Most pages are grayed out](#grayed-out) |
| A red or yellow status bar on the **ADK** page | [ADK is not installed](#adk-not-installed), [version is unsupported](#adk-unsupported), [add-on is missing](#winpe-missing), [add-on needs repair](#winpe-repair) |
| An ADK installation that ends with a message | [ADK operation failed](#adk-failed), [canceled](#adk-canceled), [finished with remaining requirements](#adk-remaining) |
| A message on **General**, or **Start** blocked by a General option | [No language packs](#no-language-packs), [passwords](#matching-passwords), [custom driver folder](#driver-folder) |
| An update that fails or is not installed | [Update check failed](#update-failed), [stays on Apply update](#update-stays), [check skipped](#update-skipped), [update dialog before media creation](media-creation.md#update-advisory) |
| A proxy test that fails | [Check the proxy settings, Connection failed](#proxy) |
| Options back to their defaults | [Settings were reset](#settings-reset) |
| An export, a link or a bug report that does not open | [Diagnostics export failed](#export-failed), [Documentation could not be opened](#docs-not-opened) |
| Foundry OSD does not close | [Foundry OSD does not close](#does-not-close) |

## Where the log is

Foundry OSD writes its log to `%ProgramData%\Foundry\Logs\Foundry.log` and keeps up to 10 files. **Settings** > **General** shows the folder as a link. Logs written by the Microsoft ADK installers are in the `Adk` subfolder.

**Settings** > **General** > **Export...** packs the Foundry OSD logs into one archive with sensitive values masked. The ADK installer logs are not part of it: add them by hand when a problem concerns the ADK. Every location is listed in [Log locations](logs-and-support.md#log-locations).

## Foundry OSD does not open <a href="#does-not-open" id="does-not-open"></a>

**Where:** At startup. No message is shown.

**Cause:**

- The Windows elevation (UAC) prompt was declined, or the account cannot approve it. Foundry OSD only runs elevated.
- A startup error.

**Fix:**

1. Start Foundry OSD again and approve the prompt with an administrator account.
2. If the window still does not appear, run the latest MSI again. See [Download and install](../start-here/download.md).

**Collect:** `Foundry.log`. A startup error is recorded as "Foundry process bootstrap failed.", "Foundry WinUI launch failed." or "Unhandled WinUI exception.".

## Most pages are grayed out <a href="#grayed-out" id="grayed-out"></a>

**Where:** Navigation pane. Only **Home**, **ADK** and **Settings** can be opened. The **ADK** item shows the badge **ADK not ready**, and **Home** shows **Requires ADK**.

**Cause:** Foundry OSD has not found a usable Windows ADK and Windows PE add-on.

**Fix:** Open **ADK** and follow the entry below that matches its status bar.

**Collect:** Nothing.

## "ADK is not installed" <a href="#adk-not-installed" id="adk-not-installed"></a>

**Where:** **ADK** page, status bar. Full text: "Install the Windows ADK Deployment Tools and the Windows PE Add-on before configuring media creation."

**Cause:** The Deployment Tools of the Windows ADK are not on this workstation.

**Fix:** Select **Install Windows ADK and Windows PE Add-on**. If the button reads **Upgrade Windows ADK and Windows PE Add-on**, Windows still lists an ADK whose files are gone; select it.

**Collect:** Nothing, unless the installation fails.

## "ADK version is unsupported" <a href="#adk-unsupported" id="adk-unsupported"></a>

**Where:** **ADK** page, status bar. Full text: "Install the supported Windows ADK 24H2 (10.1.26100.9457) release."

**Cause:** The installed ADK is older or newer than the versions Foundry OSD accepts. **Readiness details** shows the **Installed version**. The rule is in [Supported versions](../reference/supported-versions.md#windows-adk).

**Fix:** Select **Upgrade Windows ADK and Windows PE Add-on** (older version) or **Downgrade Windows ADK and Windows PE Add-on** (newer version). Both remove the installed ADK and add-on, then install the supported version. If another tool on the workstation needs the newer ADK, use a different workstation for Foundry OSD.

**Collect:** Nothing, unless the installation fails.

## "Windows PE Add-on is missing" <a href="#winpe-missing" id="winpe-missing"></a>

**Where:** **ADK** page, status bar. Full text: "Install the Windows PE Add-on for the installed Windows ADK."

**Cause:** A supported ADK is installed without the Windows PE add-on.

**Fix:** Select **Install Windows ADK and Windows PE Add-on**. Only the add-on is installed.

**Collect:** Nothing, unless the installation fails.

## "Windows PE Add-on needs repair" <a href="#winpe-repair" id="winpe-repair"></a>

**Where:** **ADK** page, status bar, with no button. The same text appears as a warning under **ADK is ready** when only one architecture is affected. Full text: "Repair the matching Windows PE Add-on with its installer. If its version differs from the ADK, uninstall the add-on first, then install the matching version. Restart Foundry after repairing or replacing the add-on."

**Cause:**

- The add-on is installed but files are missing. **Readiness details** shows which architecture, for example `(x64: WinPE files available, ARM64: WinPE files missing)`.
- The add-on and the ADK have different versions.

**Fix:**

1. If the add-on has the same version as the ADK, repair it with its Microsoft installer: in the installed apps list of Windows, select the Windows PE add-on and start its change or repair action. Then restart Foundry OSD.
2. If its version differs from the ADK, uninstall the add-on and restart Foundry OSD. The status becomes **Windows PE Add-on is missing**: select **Install Windows ADK and Windows PE Add-on**.
3. Foundry OSD installs add-on version `10.1.26100.9457`. If your ADK has a later revision, install the add-on of that revision from [Microsoft's ADK page](https://learn.microsoft.com/windows-hardware/get-started/adk-install) instead.

If only the architecture you do not use is incomplete, you can ignore the warning.

**Collect:** `Foundry.log`. It contains a line starting with "ADK status refreshed." that lists each check.

## "ADK operation failed" <a href="#adk-failed" id="adk-failed"></a>

**Where:** Dialog titled **Operation complete**, after an installation started from the **ADK** page.

**Cause:**

- An installer could not be downloaded: no Internet access, or a proxy that blocks Microsoft's download site.
- A Microsoft installer ended with an error, for example because another Windows installation was running.

**Fix:**

1. Check Internet access. If the workstation uses a proxy, set it in **Settings** > **Proxy** and select **Test connection**.
2. Wait for any other installation to finish, or restart Windows.
3. Select the button again.

**Collect:** `Foundry.log` (search for "ADK operation failed"), and the files of `%ProgramData%\Foundry\Logs\Adk`.

## "Windows ADK operation canceled." <a href="#adk-canceled" id="adk-canceled"></a>

**Where:** **Operation in progress** dialog, during an installation started from the **ADK** page.

**Cause:** The elevation prompt of the Microsoft installer was declined.

**Fix:** Select the button again and approve the prompt.

**Collect:** Nothing.

## "Installation finished. Review the remaining ADK requirements." <a href="#adk-remaining" id="adk-remaining"></a>

**Where:** Dialog shown at the end of an installation started from the **ADK** page.

**Cause:** The installers ended without error, but Foundry OSD still does not find everything it needs.

**Fix:**

1. Read the status bar of the **ADK** page and follow the matching entry above.
2. If the status does not change, restart Windows, then open Foundry OSD again.

**Collect:** `Foundry.log` and the files of `%ProgramData%\Foundry\Logs\Adk`.

## "No WinPE language packs were detected for the selected architecture." <a href="#no-language-packs" id="no-language-packs"></a>

**Where:** **General** page, under **WinPE boot language**, whose list is disabled. On **Start**, the related reasons are "WinPE boot language is not selected." and "Selected WinPE boot language is not available for the selected architecture.".

**Cause:**

- The Windows PE files of the architecture selected in **Architecture and signature** are missing.
- The architecture was changed and the language chosen before is not installed for the new one.
- **General** was never opened, so no language has been chosen yet.

**Fix:**

1. Open **General** and choose a **WinPE boot language**.
2. If the list is disabled, open **ADK** and check **Readiness details**. Repair the add-on as described in ["Windows PE Add-on needs repair"](#winpe-repair), or select the other architecture.

**Collect:** Nothing.

## "Enter matching passwords with at least 8 characters." <a href="#matching-passwords" id="matching-passwords"></a>

**Where:** **General** page, under **Deployment password**. **Review and start** is disabled. On **Start**, the reason reads "Deploy configuration generation is not ready.".

**Cause:** **Password protection** is on and the two password fields are empty, different, or shorter than 8 characters. The fields are also empty after Foundry OSD restarts when **Remember passwords** is off.

**Fix:** Type the Deployment password in both fields, or turn **Password protection** off. See [General](../foundry-osd/general.md#password-protection).

**Collect:** Nothing.

## "Custom driver folder does not exist." <a href="#driver-folder" id="driver-folder"></a>

**Where:** **Start** page, **Driver options** row. A second form is "Custom driver folder does not contain .inf files.".

**Cause:** The folder set in **General** > **Driver options** > **Custom driver folder** was moved, is on a drive that is not connected, or holds only packed installers.

**Fix:** In **General**, select a folder that contains extracted drivers with their `.inf` files, or empty the field.

**Collect:** Nothing.

## "Update check failed" <a href="#update-failed" id="update-failed"></a>

**Where:** **Settings** > **Update app**. The text continues with the reason: "Update check failed: \<reason\>". Related messages: "Update download failed: \<reason\>", with a **Retry** button, and **Update could not be applied** with "The update could not be applied: \<reason\>".

**Cause:**

- GitHub cannot be reached, directly or through the proxy.
- The download was interrupted.
- The update could not be handed over to the installer.

**Fix:**

1. In **Settings** > **Proxy**, select **Test connection**.
2. Select **Check for updates** or **Retry**.
3. Close every other Foundry OSD window, then select **Apply update**.
4. If it still fails, install the latest MSI over the existing installation. See [Download and install](../start-here/download.md).

**Collect:** `Foundry.log` (search for "Foundry update").

## The update stays on "Apply update" <a href="#update-stays" id="update-stays"></a>

**Where:** **Update** item at the bottom of the navigation pane, after you closed and reopened Foundry OSD.

**Cause:** A second Foundry OSD window was open, so the update was not installed when you closed Foundry OSD. At the next start it is offered again as **Apply update**.

**Fix:** Close every Foundry OSD window, or select **Apply update**.

**Collect:** `Foundry.log`. It contains "Prepared Foundry update not scheduled because another Foundry instance is running.".

## "Update check skipped" <a href="#update-skipped" id="update-skipped"></a>

**Where:** **Settings** > **Update app**. Full text: "Update check skipped because Foundry OSD is not running from a Velopack installation."

**Cause:** Foundry OSD was copied from another computer or started from a folder, instead of being installed with the MSI.

**Fix:** Install Foundry OSD with the MSI. See [Download and install](../start-here/download.md).

**Collect:** Nothing.

## "Check the proxy settings" and "Connection failed" <a href="#proxy" id="proxy"></a>

**Where:** **Settings** > **Proxy**, after **Test connection** or **Apply**.

**Cause:**

| Text under the title | Cause |
| --- | --- |
| "Enter a valid HTTP or HTTPS proxy address." | The address is empty, or contains a path, a query or credentials. |
| "Enter a username and password for explicit proxy authentication." | **Username and password** is selected and a field is empty. |
| "The proxy rejected the configured authentication." | The proxy refused the credentials or the authentication mode. |
| Another network error, in English | One of `github.com`, `login.microsoftonline.com` or `graph.microsoft.com` did not answer within 15 seconds. |

**Fix:**

1. Type the proxy address as a host name or a URL without a path, for example `proxy.contoso.com`, and the port in the **Port** field.
2. Choose the **Authentication** your proxy expects.
3. Check with your network team that the three hosts are allowed. See [Network endpoints](../reference/network-endpoints.md).

**Collect:** `Foundry.log` (search for "Proxy connection test failed" or "Failed to apply proxy settings").

## Settings were reset <a href="#settings-reset" id="settings-reset"></a>

**Where:** **Settings**. Language, theme and proxy are back to their defaults, and **Enable telemetry** and **Enable remote diagnostics** are on again. No message is shown.

**Cause:** The settings file could not be read. Foundry OSD renamed it and started from the defaults.

**Fix:** Set the options again, including the telemetry and remote diagnostics switches if you had turned them off.

**Collect:** `%ProgramData%\Foundry\Settings\appsettings.json.invalid` and `Foundry.log`, which contains "App settings file was invalid and defaults were restored.".

## "Diagnostics export failed" <a href="#export-failed" id="export-failed"></a>

**Where:** Dialog after **Settings** > **General** > **Export...**. Full text: "Diagnostics could not be exported. Check the log for details."

**Cause:** The archive could not be written to the destination you chose.

**Fix:** Run the export again and choose another folder, for example your Documents folder.

**Collect:** `Foundry.log` (search for "Support bundle export failed"). Copy the file by hand, since the export is what fails.

## "Documentation could not be opened" <a href="#docs-not-opened" id="docs-not-opened"></a>

**Where:** Dialog after selecting **Documentation**. For **Report a bug**, the title is **Could not open bug report**.

**Cause:** Windows has no default browser to open the link.

**Fix:** Copy the address shown in the dialog and open it in a browser, on this workstation or another one.

**Collect:** Nothing.

## Foundry OSD does not close <a href="#does-not-close" id="does-not-close"></a>

**Where:** When you close the window.

**Cause:**

- An operation is running. Foundry OSD does not close while the **Operation in progress** dialog is shown.
- The current configuration could not be saved. A dialog titled **Foundry settings** then shows ["Some passwords or source files are missing."](foundry-osd/settings-backup-and-sync.md#missing-passwords) or ["Couldn't complete this action."](foundry-osd/settings-backup-and-sync.md#could-not-complete).

**Fix:** Wait for the operation to end. In the dialog, select **Cancel** to go back and fix the problem, or **Close** to quit with the last saved state.

**Collect:** `Foundry.log`.
