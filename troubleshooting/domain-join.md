# Domain Join troubleshooting

Start from where the problem shows: in Foundry OSD while preparing the media, in the Deploy wizard, or in installed Windows after the restart.

## Where to look

| Where | What it tells you |
| --- | --- |
| The Domain Join page in Foundry OSD | Messages under each card and the **Status** column of the Domains table say what is missing before media can be created. |
| The **Domain join** step and the summary in Foundry Deploy | Messages under each field, and the domain and OU the join will use. |
| The Foundry console during Windows setup | One line summarizes the join, for example `Domain - Join: Failed; Placement: Skipped; Membership: Skipped; Restart: NotRequired; Cleanup: Disposed`. |
| `%SystemRoot%\Temp\Foundry\State\PreOobe\domain-join-result.json` | The same outcome with a reason (`failureCode`) and numeric error codes. |
| `%SystemRoot%\Temp\Foundry\Logs\PreOobe\Foundry.PostInstall.log` | The detailed log of the join. |

To read the files while Windows setup is still on screen, press **Shift+F10** to open a command prompt. See [which files to collect](logs-and-support.md#domain-join-evidence) before asking for help.

Each part of the outcome (join, placement in the OU, membership) has its own state:

| State | Meaning |
| --- | --- |
| Succeeded | That part completed. |
| Failed | That part failed; the reason is in the result file. |
| Skipped | That part did not apply, or was not attempted because an earlier part failed. |
| Unknown | The operation was interrupted, so the directory may or may not have been changed. |
| Unverified | The operation was requested but its result could not be confirmed. |
| NotStarted | Nothing was recorded for that part yet. |

## Media is not ready or input is rejected

**In Foundry OSD**

| Message or symptom | What to do |
| --- | --- |
| A domain shows a missing password or account in **Status** | Select the domain and choose **Edit**, or complete the **Shared join account** card. Each domain needs an account written as `DOMAIN\user` or `user@domain`, with its password. |
| Password protection is required | Zero-touch needs [Protected deployment](../foundry-osd/general.md#protected-deployment): enable it on the General page and set the technician password. |
| At least one domain is required | Zero-touch needs one listed domain or more. Interactive can list none. |
| The passwords are empty after importing a profile | A `.foundryprofile` export never contains the join passwords. Enter them again, and set the technician password on this PC. |
| A domain cannot be renamed | Its name is locked while it lists OUs. Remove its OUs first, or add the new domain and remove the old one. |
| An OU is refused | The distinguished name must be an organizational unit of the selected domain: it starts with `OU=` and ends with that domain's `DC=` parts. A container such as `CN=Computers` is not accepted. |
| **Import from domain** finds nothing or fails | The search uses your Windows account. It fails when this computer cannot reach the domain or when the domain does not trust your account. Add the OUs manually instead. |

**In the Deploy wizard**

| Message or symptom | What to do |
| --- | --- |
| **Next** is unavailable on the Domain join step | Correct the fields that show a message: a valid domain name, an account written as `DOMAIN\user` or `user@domain`, a password, and an OU when the domain lists several. |
| Another domain cannot be typed | When the media lists domains, only those can be joined. Ask the administrator to add the domain in Foundry OSD. |
| The typed OU is refused | A typed OU is only offered for a domain without listed OUs. It must start with `OU=` and belong to the chosen domain. |
| The Domain join step is not shown | On Zero-touch media this is expected when the media lists one domain and at most one OU for it: there is nothing to choose. |
| The Domain join step appears on Zero-touch media | The media lists several domains, or several OUs for the domain. To avoid the step, the administrator lists one domain and at most one OU. |
| "The domain join information is missing or not valid. Deployment has not started." | Return to the Domain join step and check the entries. With a custom answer file, check its computer name as described below. |
| The summary says the edition cannot join a domain | Windows Home editions cannot join a domain. Choose another edition, or continue without the join. See [supported editions](../reference/supported-versions.md#domain-join). |

**With a custom answer file or a custom Windows image**

The answer file must set exactly one fixed computer name in its `specialize` pass; a missing name, a `*` wildcard or several names are refused, and Foundry does not fill the name from the wizard. Neither the answer file nor the image may contain a `Microsoft-Windows-UnattendedJoin` component, because Foundry performs the join itself. When Foundry finds such a conflict in the applied image, it skips the join with a warning in the deployment summary and the installation continues.

## Windows asks who will use the device

Setup ends on the sign-in screen only after Foundry has confirmed that the computer is a member of the domain. When it stops on **Who's going to use this device?** instead, the join failed or could not be confirmed. Create a local account to reach the desktop, then follow [Joining fails](#joining-fails).

The page also appears with a [custom answer file](../foundry-osd/customization/unattend.md) that creates no account, because that file decides which setup screens appear. Create the account in that file.

## Joining fails

Open `domain-join-result.json` and read `failureCode` and the numeric codes under `join`:

| What the result shows | Likely cause | What to do |
| --- | --- | --- |
| `DomainUnavailable` with `ldapErrorCode` 49, or `JoinFailed` with `nativeErrorCode` 1326 | The directory refused the account or its password. | Check the account and password. On Zero-touch media, correct them in Foundry OSD and create the media again. |
| `ReadinessTimeout`, or `DomainUnavailable` with another code | No domain controller answered within two minutes. | Check the network driver and connection in installed Windows, the DNS servers given to the computer, and its date and time. A working Internet connection does not prove that the domain is reachable. |
| `JoinFailed` with `nativeErrorCode` 1355 | The domain was not found. | Check the domain name and DNS. |
| `JoinFailed` with `nativeErrorCode` 5 | Access denied: the account is not allowed to join computers. | Ask the Active Directory administrator to review the account's permissions. |
| `JoinFailed` with `nativeErrorCode` 2224 or 2732 | A computer account with this name exists and the join account may not reuse it. | Windows restricts the reuse of existing computer accounts; see Microsoft's [KB5020276 guidance](https://support.microsoft.com/en-us/servicing/os/windows/2022/10/kb5020276-netjoin-domain-join-hardening-changes). Use the account that created the computer object, have the administrator allow the reuse, or choose another computer name. Foundry does not delete or recreate the existing account. |
| `ComputerNameMismatch` | The expected computer name could not be applied. | Check the computer name set by machine naming or by the custom answer file. |
| `CredentialUnavailable` or `ContextMismatch` | The prepared credentials could not be read, or were not made for this domain. | Deploy the computer again. If it repeats on Zero-touch media, create the media again. |

Foundry never repeats a failed join by itself, so a wrong password cannot lock the account. After fixing the cause, either deploy the computer again or join it manually in Windows, from **Settings > Accounts > Access work or school** or with `Add-Computer`.

## Joined but the OU is wrong or unverified

The console line and the result file report the join and the placement in the OU separately. A successful join still restarts Windows and is checked even when the placement fails.

| Console message | Meaning | What to do |
| --- | --- | --- |
| Domain joined in the default location; target OU not found | The OU does not exist in the directory (`OrganizationalUnitNotFound`). | Correct the OU in Foundry OSD for future media, and have the administrator move the computer account. |
| Domain joined; target OU placement failed | The account could not be created in or moved to the OU, usually for lack of permission on that OU. | Have the administrator check the join account's permissions on the OU and move the computer account. |
| Domain joined; target OU placement not confirmed | The move was requested but Foundry could not read the result back. | Have the administrator check where the computer account is. |

An existing computer account is moved only when Foundry can confirm it is the same computer object; otherwise it is left in place and the placement is reported. Without an OU, an existing account is never moved. Being a member of the domain does not prove that the account is in the intended OU.

## Interrupted work or restart is pending

If the computer loses power or the join takes longer than five minutes, the console shows **Domain join outcome unknown; membership is checked after restart**. The directory may or may not have been changed, so Foundry does not try the join again.

- Keep power and network connected until Windows has restarted and setup has finished.
- After the restart, look at **Membership**. **Succeeded** means the computer is in the domain; check the OU with the administrator. Otherwise join the computer manually or deploy it again.
- Do not restart the computer by hand to make the warning disappear.

## Credential cleanup remains Pending

During the join, the account and password are kept in a temporary file, readable only by SYSTEM and administrators:

`%SystemRoot%\Temp\Foundry\Payloads\DomainJoin\<operation-id>\credentials.bin`

Foundry deletes it as soon as the join has used it; the console then shows **Cleanup: Disposed**. **Cleanup: Pending** means the file could not be deleted yet, for example because the join was interrupted. Foundry tries again at the next start.

If it is still pending when setup has finished, have an administrator delete that folder once the computer is no longer running any Foundry action. Never open, copy or attach this file to a support request: it contains the join password.

## Ask for help

Collect the result file and the log listed in [Domain Join evidence](logs-and-support.md#domain-join-evidence). They contain no account or password, but they do contain the domain, computer and OU names; remove them if they are sensitive.
