# Foundry OSD application troubleshooting

This page covers problems with the Foundry OSD application on the administrator workstation: installing and starting it, the ADK page, the General page, updates, Settings, and Settings backup and sync. For a problem that appears while media is being created, see [Media creation](media-creation.md).

## Find your symptom

| What you see | Go to section |
| --- | --- |
| The window never appears, or closes at once | [Foundry OSD does not open](#does-not-open) |
| Pages grayed out, badge **ADK not ready** | [Most pages are grayed out](#grayed-out) |
| A red or yellow status bar on the **ADK** page | [ADK is not installed](#adk-not-installed), [version is unsupported](#adk-unsupported), [add-on is missing](#winpe-missing), [add-on needs repair](#winpe-repair) |
| An ADK installation that ends with a message | [ADK operation failed](#adk-failed), [canceled](#adk-canceled), [finished with remaining requirements](#adk-remaining) |
| A message on **General**, or **Start** blocked by a General option | [No language packs](#no-language-packs), [passwords](#matching-passwords), [custom driver folder](#driver-folder) |
| An update that fails or is not installed | [Update check failed](#update-failed), [stays on Apply update](#update-stays), [check skipped](#update-skipped), [update dialog before media creation](#update-advisory) |
| A proxy test that fails | [Check the proxy settings, Connection failed](#proxy) |
| Options back to their defaults | [Settings were reset](#settings-reset) |
| An export, a link or a bug report that does not open | [Diagnostics export failed](#export-failed), [Documentation could not be opened](#docs-not-opened) |
| A message under the **Settings backup and sync** card | [Couldn't complete this action](#could-not-complete), [can't open this settings format](#settings-format), [cannot unlock](#cannot-unlock), [passwords or files missing](#missing-passwords) |
| A shared folder problem | [Folder cannot be reached](#folder-unreachable), [saved versions changed](#versions-changed), [folder name in use](#folder-in-use), [shared settings deleted](#shared-deleted) |
| Another **Settings backup and sync** message | [Old passwords not removed](#cleanup-pending), [check the required fields](#required-fields), [files missing after import](#files-missing) |
| Foundry OSD does not close | [Foundry OSD does not close](#does-not-close) |

## Where the log is

Foundry OSD writes its log to `%ProgramData%\Foundry\Logs\Foundry.log` and keeps up to 10 files. **Settings** > **General** shows the folder as a link. Logs written by the Microsoft ADK installers are in the `Adk` subfolder.

**Settings** > **General** > **Export...** packs the Foundry OSD logs into one archive with sensitive values masked. The ADK installer logs are not part of it: add them by hand when a problem concerns the ADK. Every location is listed in [Log locations](logs-and-support.md#log-locations).

## Foundry OSD does not open <a href="#does-not-open" id="does-not-open"></a>

**Where:** At startup. No message is shown.

**Cause:**

- The Windows elevation (UAC) prompt was declined, or the account cannot approve it. Foundry OSD only runs elevated.
- A startup error. The log then contains "Foundry process bootstrap failed.", "Foundry WinUI launch failed." or "Unhandled WinUI exception.".

**Fix:**

1. Start Foundry OSD again and approve the prompt with an administrator account.
2. If the window still does not appear, run the latest MSI again. See [Download and install](../start-here/download.md).

**Collect:** `Foundry.log`.

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
- A Microsoft installer ended with an error, often because another Windows installation was running.

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

**Cause:** A second Foundry OSD window was open, so the update was not installed on closing or at startup.

**Fix:** Close every Foundry OSD window, or select **Apply update**.

**Collect:** `Foundry.log`. It contains "Prepared Foundry update not scheduled because another Foundry instance is running.".

## "Update check skipped" <a href="#update-skipped" id="update-skipped"></a>

**Where:** **Settings** > **Update app**. Full text: "Update check skipped because Foundry OSD is not running from a Velopack installation."

**Cause:** Foundry OSD was copied from another computer or started from a folder, instead of being installed with the MSI.

**Fix:** Install Foundry OSD with the MSI. See [Download and install](../start-here/download.md).

**Collect:** Nothing.

## "Update Foundry OSD before creating boot media" <a href="#update-advisory" id="update-advisory"></a>

**Where:** Dialog shown on **Start** when you select **Create ISO**, **Create USB** or **Update USB**.

**Cause:** A newer Foundry OSD is available. This is advice, not an error.

**Fix:** Select **Apply update** to install it and restart Foundry OSD, then create the media. **View update** appears instead while the update is not downloaded yet. **Create anyway** builds the media with the current version.

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

1. Type the proxy address as a host name or a URL without a path, for example `proxy.contoso.com`, and the port separately.
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

## "Couldn't complete this action. Check the file, its password, your access permissions, and your network connection." <a href="#could-not-complete" id="could-not-complete"></a>

**Where:** After an import, an export, a connection or a synchronization, under the **Settings backup and sync** card. Sync status: **Unable to synchronize**.

**Cause:** One message covers several causes. The most frequent:

- The file password or the connection password is wrong.
- The file is damaged, or is larger than 16 MiB.
- A file of the wrong kind was chosen: an exported configuration where a connection file is expected, or the connection file of another shared configuration.
- A file selected in the configuration, such as an answer file or a certificate, is larger than 4 MiB.
- The connection file was saved inside the shared folder.

**Fix:**

1. Check which file you chose and type its password again.
2. Check that you can read and write in the shared folder.
3. Read the reason in the log.

**Collect:** `Foundry.log`. Search for "Profile operation failed"; the line names the operation and the reason.

## "Foundry can't open this settings format. Update Foundry and try again." <a href="#settings-format" id="settings-format"></a>

**Where:** After an import, a connection or a synchronization, under the **Settings backup and sync** card. Sync status: **Unable to synchronize**.

**Cause:** The file or the shared configuration was written by a newer Foundry OSD than the one on this workstation.

**Fix:** Update Foundry OSD on this workstation, then try again. In a team, update every workstation: as soon as one updated workstation synchronizes, the others cannot until they are updated too.

**Collect:** The Foundry OSD version of both workstations, from **About**.

## "Windows cannot unlock these saved settings or their synchronization access for your account on this PC." <a href="#cannot-unlock" id="cannot-unlock"></a>

**Where:** When a configuration is selected or synchronized, under the **Settings backup and sync** card. Sync status: **Unable to synchronize**, and the **Synchronize** button becomes **Restore access**.

**Cause:** The key that protects the configuration in Windows Credential Manager is missing: another Windows account is signed in, saved credentials were cleaned, or the data was copied from another PC.

**Fix:**

1. Sign in with the Windows account that created the configuration.
2. For a shared configuration, select **Restore access** and choose its connection file.
3. Otherwise, import the configuration again from an exported file.

**Collect:** `Foundry.log`.

## "Some passwords or source files are missing. Add them before saving or sharing passwords." <a href="#missing-passwords" id="missing-passwords"></a>

**Where:** Under the **Settings backup and sync** card, with the sync status **Needs attention**, and in a dialog titled **Foundry settings** when you close Foundry OSD.

**Cause:** **Remember passwords** is on, or the configuration is shared with its passwords, and a password or a file that a feature requires has not been provided.

**Fix:** Complete the page concerned, or turn **Remember passwords** off. In the closing dialog, **Cancel** returns to the app so that you can complete it; **Close** quits and keeps the last saved state.

**Collect:** Nothing.

## "The shared folder cannot be reached. Check its address and your network access. Your changes are kept on this PC." <a href="#folder-unreachable" id="folder-unreachable"></a>

**Where:** During a synchronization, under the **Settings backup and sync** card. Sync status: **Folder unavailable**. A second form is "Another operation is using this shared folder. Try again shortly. Your changes are kept on this PC.".

**Cause:**

- The network, the VPN or the file server is not available.
- Your account is not allowed to read and write in the folder. Foundry OSD reports a refused access the same way.
- Another workstation is synchronizing at this moment.

**Fix:**

1. Open the folder in File Explorer with the same account and check that you can create a file in it.
2. Select **Synchronize** again after a moment.

**Collect:** `Foundry.log`.

## "The saved versions in the shared folder have changed unexpectedly. Restore from a backup you trust." <a href="#versions-changed" id="versions-changed"></a>

**Where:** During a synchronization, under the **Settings backup and sync** card. Sync status: **Unable to synchronize**.

**Cause:** Files of the shared folder were deleted, or replaced by older copies.

**Fix:**

1. Restore the shared folder from the most recent backup you trust.
2. If you have no backup, choose the workstation that holds the right settings, select **Disconnect** on it, then share the configuration again in a new, empty folder and give the new connection file to the other workstations.

Do not edit or delete files in the shared folder by hand.

**Collect:** `Foundry.log`.

## "This folder name is already in use. Connect using a connection file, or go back and choose another name." <a href="#folder-in-use" id="folder-in-use"></a>

**Where:** **Share this configuration** dialog. A second form is "Choose a new, empty subfolder in the shared folder. Existing files will stay unchanged.".

**Cause:** The folder that would be created for this configuration already exists, or the folder you chose is not empty.

**Fix:** To join the configuration that already uses this name, select **Connect** and choose its connection file. To share a new one, select **Choose another name**.

**Collect:** Nothing.

## "These shared settings were deleted. Choose Duplicate to keep your changes on this PC." <a href="#shared-deleted" id="shared-deleted"></a>

**Where:** Under the **Settings backup and sync** card. Sync status: **Needs attention**.

**Cause:** Someone deleted the shared configuration with **Also delete for everyone**.

**Fix:** Select **More options** > **Duplicate** to keep a local copy, then remove the old entry with **Delete from this PC**.

**Collect:** Nothing.

## "Some old saved passwords or access keys could not be removed. Try again when Windows Credential Manager is available." <a href="#cleanup-pending" id="cleanup-pending"></a>

**Where:** Under the **Settings backup and sync** card. Sync status: **Needs attention**.

**Cause:** Windows Credential Manager did not answer while Foundry OSD was removing saved passwords.

**Fix:** Try the same action again later, or after restarting Windows.

**Collect:** `Foundry.log`.

## "Check the required fields. If a network folder is requested, use a path such as \\\\server\\share\\folder. If you enter a file password twice, make sure both entries match." <a href="#required-fields" id="required-fields"></a>

**Where:** Export, import, share and connect dialogs of **Settings backup and sync**. Related messages: "Use a name without special characters or a trailing dot or space." and "The passwords do not match.".

**Cause:**

- A name or a password is empty.
- The shared folder is given as a mapped drive letter such as `Z:\Foundry`. Only a network path starting with `\\` is accepted.
- The configuration name cannot be used as a folder name.
- The two passwords differ.

**Fix:** Correct the field. For the shared folder, type the network path, for example `\\server\share\Foundry`.

**Collect:** Nothing.

## "\<number\> files are missing or were not included. Select their locations before creating media." <a href="#files-missing" id="files-missing"></a>

**Where:** **Settings backup and sync**, in the preview shown when you import a configuration or connect to a shared one.

**Cause:** The configuration refers to files, such as certificates or answer files, that were not included in the export or are not on this workstation.

**Fix:** Finish the import, then open each page marked **Needs attention** and select the file again.

**Collect:** Nothing.

## Foundry OSD does not close <a href="#does-not-close" id="does-not-close"></a>

**Where:** When you close the window.

**Cause:**

- An operation is running. Foundry OSD does not close while the **Operation in progress** dialog is shown.
- The current configuration could not be saved. A dialog titled **Foundry settings** then shows ["Some passwords or source files are missing."](#missing-passwords) or ["Couldn't complete this action."](#could-not-complete).

**Fix:** Wait for the operation to end. In the dialog, select **Cancel** to go back and fix the problem, or **Close** to quit with the last saved state.

**Collect:** `Foundry.log`.
