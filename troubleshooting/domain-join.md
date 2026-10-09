# Domain Join troubleshooting

Find what you see, then go to its section. Sections follow the order of a deployment.

| What you see | Go to |
| --- | --- |
| Foundry OSD does not save a domain or an OU | [Refused entry](#entry-refused) |
| **Import from domain** ends with a message | [Import](#import-adds-no-ous) |
| A domain is not **Ready** | [Readiness](#page-not-ready) |
| **Next** unavailable, or no **Domain join** step | [Wizard step](#step-blocked) |
| "The selected answer file is unavailable..." | [Answer file](#answer-file-refused) |
| "The domain join information is missing..." | [No start](#join-information-not-valid) |
| The deployment fails on the answer file | [Deployment stops](#deployment-stops) |
| No `Domain` lines after the restart | [Skipped join](#join-skipped) |
| `Join: NotStarted`, `Cleanup: Pending` | [Not started](#notstarted-and-pending) |
| `Join: Failed`, **Who's going to use this device?** | [Failed join](#join-failed) |
| `Membership: Failed` or `Unverified` | [Membership](#membership-not-confirmed) |
| "Domain joined..." in yellow | [Wrong OU](#wrong-ou) |
| "Domain join outcome unknown..." | [Unknown](#join-outcome-unknown) |
| `Cleanup: Pending` after setup | [Cleanup](#cleanup-pending) |

Where each log is and how to export it is in [Logs and support information](logs-and-support.md). The files written on the deployed device are listed with their paths in [Reference](#reference).

## Foundry OSD refuses a domain or an OU <a href="#entry-refused" id="entry-refused"></a>

- **Where:** the domain and OU dialogs of a Domain Join page. The dialog stays open with a message.
- **Cause:**
  - "Enter a valid domain name, such as corp.contoso.com.": the name is not a DNS name. A NetBIOS name such as `CONTOSO` is refused.
  - "The OU was not added. Check the display name and the distinguished name. The OU must belong to the selected domain.": the display name is empty or longer than 120 characters, the distinguished name does not start with `OU=` (`CN=Computers` is refused), or it does not end with the `DC=` parts of the selected domain.
  - Other messages name a limit (32 domains, 1,024 OUs per domain), a domain already listed, or OUs to remove before a domain can be renamed.
- **Fix:** correct the entry as the message says. An OU that is already listed is not refused: the dialog closes and nothing is added.
- **Collect:** the exact message and the value you typed.

## Import from domain adds no OUs <a href="#import-adds-no-ous" id="import-adds-no-ous"></a>

- **Where:** the line under the **Organizational units** commands, after **Import from domain**.
- **Cause:** the search uses your Windows account and stops after 60 seconds.
  - "The domain could not be searched. Check that this computer can reach it and that your Windows account can read it, or add OUs manually.": no domain controller was found, or the directory refused your account.
  - "The search timed out. You can add OUs manually.": no answer in time.
  - "The OUs were not added. They belong to a different domain than the selected one." or "Two OUs share the same internal ID. Remove one and add it again.": the result does not fit the list.
- **Fix:** check from the workstation that the domain name resolves and that a domain controller answers on TCP 389. Otherwise select **Add** and type each OU.
- **Collect:** the Foundry OSD log line that starts with `Domain destination discovery ended.` See [Log locations](logs-and-support.md#log-locations).

A different case: when the import dialog opens with "Only some of the OUs could be listed. You can still add the ones shown.", the domain holds more than 4,096 OUs or answered partially. The OUs shown can be added; type the missing ones with **Add**.

## The Zero-touch Domain Join page is not ready <a href="#page-not-ready" id="page-not-ready"></a>

- **Where:** **Domain Join > Zero-Touch**; on **Start**, **Zero-Touch** under **Domain Join** reads **Needs attention**. The Interactive Domain Join page has no **Status** column.
- **Cause:**
  - **Status** shows "Enter the shared join account, or give this domain its own account.", "Enter the account as DOMAIN\user or user@domain." or "Enter the account password.", possibly cut with an ellipsis: that domain lacks an account or a password. Join passwords are always empty after you import a configuration from a file.
  - "Enter a valid account first, then check the password length.": the password was typed before a valid account, or exceeds 2,560 bytes, and was not kept.
  - "Turn on password protection on the General page and set the deployment password." and "Add at least one domain." are not shown in **Status**: every domain can read **Ready** while media creation is still blocked.
- **Fix:** complete **Shared join account**, account first and then password, or select the domain and select **Edit** under **Domains**. For the last two messages, turn on [Password protection](../foundry-osd/general.md#password-protection) or add a domain.
- **Collect:** the message shown. Never send an account password.

## The Domain join step does not let you continue <a href="#step-blocked" id="step-blocked"></a>

- **Where:** Foundry Deploy, **Domain join** step.
- **Cause:**
  - **Next** is unavailable: a required field is empty, an OU is not chosen, or a field shows a message.
  - Another domain cannot be typed: media that lists domains joins only those.
  - No field to type an OU: it exists only on interactive media, for a domain without listed OUs.
  - The step is missing: expected on zero-touch media with one domain and at most one OU, and for a Windows Home edition.
- **Fix:** complete or correct the fields. For a domain or an OU the media does not offer, the administrator adds it in Foundry OSD and creates the media again.
- **Collect:** what **Summary** shows under **Domain join**.

## "The selected answer file is unavailable, invalid, or incompatible with the selected Windows architecture. Choose another file or rebuild the media." <a href="#answer-file-refused" id="answer-file-refused"></a>

- **Where:** Foundry Deploy, when you select a custom answer file on **Target device** and when you start the deployment.
- **Cause:** the message does not name Domain Join. On Domain Join media it also means that the file does not set exactly one valid computer name in its `specialize` pass, or contains a `Microsoft-Windows-UnattendedJoin` component.
- **Fix:** select another file, or correct it in Foundry OSD and create the media again. See [Unattend](../foundry-osd/customization/unattend.md#what-a-custom-answer-file-overrides) for the rules, and [A message under Answer file](deployment/before-deployment-starts.md#answer-file-rejected) for the causes unrelated to Domain Join.
- **Collect:** the name of the selected file and `FoundryDeploy.log`. Do not attach the answer file.

## "The domain join information is missing or not valid. Deployment has not started." <a href="#join-information-not-valid" id="join-information-not-valid"></a>

- **Where:** Foundry Deploy, when you start the deployment. Nothing has been erased.
- **Cause:** the computer name is not valid, the join account on zero-touch media could not be unlocked or decrypted, or the media is inconsistent.
- **Fix:** check the computer name on **Target device**. On zero-touch media, restart the device and enter the Deployment password again. If it repeats, create the media again.
- **Collect:** `FoundryDeploy.log`. If present, the line `Domain join preparation failed before deployment start. FailureCode=<code>` names the reason.

## The deployment stops on the answer file <a href="#deployment-stops" id="deployment-stops"></a>

- **Where:** Foundry Deploy, during the deployment.
- **Cause:**
  - "The custom answer-file computer name differs from the computer name confirmed for the domain join.": the answer file changed after you confirmed.
  - "Post-installation staging failed. Verify the runtime, payloads and Windows answer file before retrying.": the answer file of the applied image, `Windows\Panther\unattend.xml`, could not be read. The same message has causes unrelated to Domain Join: see [Post-installation staging](deployment/after-disk-erase.md#post-installation-staging).
- **Fix:** create the media again, or correct the answer file in the custom image, then redeploy.
- **Collect:** `FoundryDeploy.log`.

"The post-installation components could not be prepared..." is not specific to Domain Join: see [Post-installation preparation](deployment/checks-and-image-download.md#post-installation-preparation-fails).

## The join was skipped <a href="#join-skipped" id="join-skipped"></a>

- **Where:** installed Windows. The **Foundry Post-installation** console shows no `Domain` lines, and `domain-join-result.json` does not exist.
- **Cause:** Foundry Deploy decided that the join could not run. When the Windows edition you selected is known to be a Home edition, **Summary** and **Confirm disk erase** announce it before the start. In the other cases the decision is taken after the image is applied and nothing on screen says so; the deployment log has the line `Domain joining skipped. Reason=<code>.`

| Reason | Meaning |
| --- | --- |
| `UnsupportedEdition` | The image is a Windows Home edition. Silent only when the edition was found after the image was applied. |
| `EmbeddedDomainJoin` | The image's `Windows\Panther\unattend.xml` contains a `Microsoft-Windows-UnattendedJoin` component. |
| `AmbiguousComputerName` | That file sets more than one computer name. |
| `ComputerNameMismatch` | That file does not set the computer name confirmed in Foundry Deploy. |

- **Fix:** for `UnsupportedEdition`, redeploy with an edition that is not a Home edition: a Home edition cannot join a domain in Windows either. For the three other reasons, correct the image or the answer file and redeploy, or join the device manually as described in [The join failed](#join-failed).
- **Collect:** the deployment log and `deployment-summary.json`, where `domainJoinStatus` is 3 (edition) or 4 (image) and `domainJoinSkipCode` is 0 to 3 in the order of the table.

## The console shows NotStarted and Pending <a href="#notstarted-and-pending" id="notstarted-and-pending"></a>

- **Where:** the **Foundry Post-installation** console, from its first second.
- **Cause:** none. `Domain - Join: NotStarted; Placement: NotStarted; Membership: NotStarted` and `Restart: NotRequired; Cleanup: Pending` are displayed before the join runs.
- **Fix:** read the lines after the restart that follows the join, when **Membership** is no longer `NotStarted`.
- **Collect:** nothing.

## The join failed <a href="#join-failed" id="join-failed"></a>

- **Where:** the console shows `Join: Failed`, `[Failed] Join domain and place computer` and "Post-installation completed with warnings. Review the execution result and logs." Unless a local account is configured, Windows then asks **Who's going to use this device?** Create a local account to reach the desktop.
- **Cause:** open `%SystemRoot%\Temp\Foundry\State\PreOobe\domain-join-result.json`; while Windows setup is on screen, **Shift+F10** opens a command prompt. Under `join`, read `failureCode`, `nativeErrorCode` and `ldapErrorCode`.

| Result file | Cause | Fix |
| --- | --- | --- |
| `DomainUnavailable` with `ldapErrorCode` 49, or `JoinFailed` with `nativeErrorCode` 1326 | The directory refused the account or its password. | Check them. On zero-touch media, correct them and create the media again. |
| `ReadinessTimeout` | No domain controller was found or reached for two minutes. With `nativeErrorCode` 1355, the domain name does not resolve. | Check the network driver, the connection and the DNS servers in installed Windows, and the domain name. |
| `DomainUnavailable` with another code | A domain controller answered, but the directory sign-in failed, several computer accounts share the name, or the OU could not be read. | Give the codes to the Active Directory administrator. |
| `JoinFailed` with `nativeErrorCode` 5 | The join account may not add computers. | Have its permissions reviewed. |
| `JoinFailed` with `nativeErrorCode` 2224 or 2732 | A computer account with this name exists and the join account may not reuse it. | See [KB5020276](https://support.microsoft.com/en-us/servicing/os/windows/2022/10/kb5020276-netjoin-domain-join-hardening-changes), or choose another computer name. |
| `JoinFailed` with another code | Windows refused the join. | Run `net helpmsg <code>` with the `nativeErrorCode`. |
| `ComputerNameMismatch` | The computer name could not be read or applied. | Check Machine naming or the answer file. |
| `CredentialUnavailable`, `ContextMismatch`, `Interrupted`, `WorkerTimeout` | The prepared account could not be read, or the join stopped before it changed anything. | Redeploy. If it repeats on zero-touch media, create the media again. |

- **Fix:** apply the fix of the matching row, then redeploy. Foundry does not repeat a join: a refused password is tried once and not retried. To join manually instead, sign in with an administrator account and join the domain from Windows Settings, under **Accounts > Access work or school**.
- **Collect:** `domain-join-result.json` and `Foundry.PostInstall.log`.

## Membership is not confirmed <a href="#membership-not-confirmed" id="membership-not-confirmed"></a>

- **Where:** `Join: Succeeded` or `Join: Unknown` with `Membership: Failed` or `Membership: Unverified`. Unless a local account is configured, Windows then asks **Who's going to use this device?**
- **Cause:** after the restart, Windows does not report the expected domain under the expected computer name (`MembershipMismatch`), or the check could not run or ran before the restart (`MembershipUnverified`).
- **Fix:** check the domain in **Settings > System > About**. If the device is not a member, join it manually as described in [The join failed](#join-failed), or redeploy.
- **Collect:** `domain-join-result.json` and `Foundry.PostInstall.log`.

## Joined, but not in the intended OU <a href="#wrong-ou" id="wrong-ou"></a>

- **Where:** a yellow line under the two `Domain` lines. The join succeeded and Windows still restarts.
- **Cause:**
  - "Domain joined in the default location; target OU not found": the OU does not exist in the directory.
  - "Domain joined; target OU placement failed": the directory refused the move, for example for lack of permission on the OU, or the existing account could not be confirmed as the same computer.
  - "Domain joined; target OU placement not confirmed": the move was requested but its result could not be read back.
- **Fix:** have the Active Directory administrator check where the computer account is and move it to the intended OU. For an OU that was not found, also correct it in Foundry OSD and create the media again. For a refused move, give the administrator the `directoryResultCode` found under `placement` in the result file.
- **Collect:** `domain-join-result.json`.

## "Domain join outcome unknown; membership is checked after restart" <a href="#join-outcome-unknown" id="join-outcome-unknown"></a>

- **Where:** a yellow line in the console, with `Join: Unknown`.
- **Cause:** the device lost power or the join exceeded five minutes. The directory may or may not have changed, so Foundry does not try again.
- **Fix:** keep power and network connected and do not restart by hand. After the restart, `Membership: Succeeded` means the device is in the domain. Otherwise join manually as described in [The join failed](#join-failed), or redeploy.
- **Collect:** `domain-join-result.json` and `Foundry.PostInstall.log`.

## Cleanup stays Pending <a href="#cleanup-pending" id="cleanup-pending"></a>

- **Where:** `Cleanup: Pending` after the join action has finished. Before that, `Pending` is normal.
- **Cause:** the temporary file that holds the join account and password could not be deleted yet. Foundry tries again at the next start.
- **Fix:** if it is still pending when setup has finished, have an administrator delete `%SystemRoot%\Temp\Foundry\Payloads\DomainJoin`. See [Security and credentials](../reference/security-and-credentials.md).
- **Collect:** nothing. Never open, copy or attach the `credentials.bin` in that folder: it contains the join password.

## Reference <a href="#reference" id="reference"></a>

<details>

<summary>Files to collect</summary>

While Windows setup is on screen, **Shift+F10** opens a command prompt. The result file and the Domain Join lines of the log hold the domain, computer and OU names, and no account or password. The log is shared with the other post-installation actions: read it before you send it.

| File | Content |
| --- | --- |
| `%SystemRoot%\Temp\Foundry\State\PreOobe\domain-join-result.json` | State of each part, `failureCode`, numeric codes |
| `%SystemRoot%\Temp\Foundry\Logs\PreOobe\Foundry.PostInstall.log` | Log of the post-installation actions, including the join |
| `%SystemRoot%\Temp\Foundry\State\Deployment\deployment-summary.json` | What Foundry Deploy decided, including a skipped join |

</details>

<details>

<summary>States of the two Domain lines</summary>

| State | Meaning |
| --- | --- |
| `NotStarted` | Not run yet. |
| `Succeeded` | That part completed. |
| `Failed` | That part failed; the reason is in the result file. |
| `Skipped` | That part did not apply, or an earlier part failed. |
| `Unknown` | Interrupted: the directory may or may not have changed. |
| `Unverified` | Requested, but the result could not be confirmed. |

**Restart** is `NotRequired`, `Required`, `Requested` or `Completed`. **Cleanup** is `Pending` or `Disposed`.

</details>
