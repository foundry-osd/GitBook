# Domain Join troubleshooting

Record the failed phase and safe numeric error family before changing the target. Collect the [domain result, execution journal and local PostInstall logs](logs-and-support.md#domain-join-evidence). A WinPE skip before staging has only Deploy's summary/logs, not an installed domain result.

| Phase state | Meaning |
| --- | --- |
| NotStarted | No completed result has been recorded for this phase. |
| Succeeded | That phase completed; check the other phases separately. |
| Failed | A known failure was recorded with an allowlisted code. |
| Skipped | The phase did not apply or was intentionally not attempted. |
| Unknown | A mutation may have occurred; do not repeat it automatically. |
| Unverified | The intended result could not be confirmed by readback or verification. |

Restart states and cleanup states are recorded separately from these phases. A completed restart or confirmed local membership does not turn an Unknown join/move into Succeeded.

## Media is not ready or input is rejected

- For Zero-touch, check the **Status** column of the Domains table: every listed domain needs a qualified account, shared or dedicated, with its password, and a usable existing [General protection password](../foundry-osd/general.md#protected-deployment). Imported portable profiles deliberately omit the join passwords.
- When the media lists domains, the technician joins one of them; another domain cannot be typed. For Interactive without listed domains, enter the domain in the Domain join step.
- A typed optional OU DN is offered only for a domain without listed OUs. It must name an organizational unit (it starts with `OU=`) inside that domain; a container such as `CN=Computers` is not accepted.
- When the retained domain lists several OUs, choose one. Without a default, nothing is preselected and a selection is required. Changing the domain replaces the OU choices with those of the new domain.
- The Domain join step appears in Zero-touch as soon as the media lists several domains, or several OUs for the retained domain. For a deployment without that step, list one domain and at most one OU.
- A domain's name cannot be changed while it lists OUs. Remove its OUs first, or add the new domain and remove the old one.
- The OU search reads the selected domain using the current Windows identity; it does not test the join account's permissions. It fails for a domain this computer cannot reach or that does not trust your account. You can still add OUs manually.
- For a custom answer file, supply one valid concrete applicable `specialize` name and remove conflicting `Microsoft-Windows-UnattendedJoin` configuration. Foundry does not fill a missing custom name from the wizard. Review arbitrary commands separately; not every scripted conflict can be detected.

Known Windows Home-family editions skip joining and continue installation. Unfamiliar/missing edition metadata needs image/native inspection. Applied-image conflicts can also skip domain work. See [supported-version limits](../reference/supported-versions.md#domain-join).

## Windows asks who will use the device

The computer has joined the domain, but setup stops on **Who's going to use this device?** instead of the sign-in screen. Windows client editions only skip that page when the answer file creates a local account. On the [OOBE page](../foundry-osd/customization/oobe.md), enable the built-in Administrator account or add a local account, then create or update the media. The Domain Join pages warn while no local account is configured. With a [custom answer file](../foundry-osd/customization/unattend.md), create the account in that file.

## Joining fails

Check installed-Windows network drivers, domain DNS, controller reachability and system time. Connect's Internet check does not confirm AD connectivity, and an OU list does not provide offline joining. Readiness checks are bounded; restore connectivity before arranging a new controlled attempt.

Ask the AD administrator to confirm the account's delegated join/reuse and directory permissions. Reuse can be restricted by Windows domain-join hardening even when a new account can be created. Review Microsoft's [KB5020276 domain-join hardening guidance](https://support.microsoft.com/en-us/servicing/os/windows/2022/10/kb5020276-netjoin-domain-join-hardening-changes) with the administrator. Foundry follows Windows' reuse decision and does not bypass hardening or delete/recreate the existing account.

## Joined but the OU is wrong or unverified

Review **Join** and **Placement** independently. Successful joining still requires the controlled restart even when placement fails; the console can report **Domain joined; target OU placement failed**.

If the requested OU does not exist in the directory, Foundry still joins the computer, in the domain's default location, and reports placement as failed with the code `OrganizationalUnitNotFound`. The console shows **Domain joined in the default location; target OU not found**. Correct the OU in Foundry OSD, then move the computer account with the administrator.

Confirm the requested DN exists and the account can read the object and the OU and perform the required same-domain placement. Existing-account moves preserve the captured GUID and require identity proof. No visible pre-join account is not proof that an account is absent, so an unproven existing object is not moved automatically.

**Unverified** can mean the placement response was acknowledged but directory readback could not confirm it. **Unknown** can mean a mutation response was lost. Have the administrator inspect the intended computer object, its GUID and actual parent. Later local domain membership does not prove OU placement, directory readback or policy application succeeded.

## Interrupted work or restart is pending

Foundry never automatically repeats an uncertain join or account move. A potentially active worker is conservatively treated as Unknown and may require one controlled restart. A repeated launch on the originating boot does not request the same restart again. Later-boot membership verification is read-only and uses no join credentials; an earlier Unknown remains Unknown even when membership is confirmed.

Keep power and required networking available through the controlled restart. Inspect the report before arranging further changes. Missing, damaged, foreign or inconsistent execution records remain integrity failures; unrelated uncertain custom actions and installer-owned restarts retain their normal stop policy.

## Credential cleanup remains Pending

The temporary plaintext file is `%SystemRoot%\Temp\Foundry\Payloads\DomainJoin\<operation-id>\credentials.bin`, protected for SYSTEM and Administrators. Do not open, copy or attach it to a support issue.

Cleanup makes at most three deletion attempts per safe immediate/final gate, with 250 ms between attempts. When a worker may still use the file on the same boot, deletion is deferred to a safe later boot. Domain-only leftovers remain Pending with a warning through later actions and finalization. Other sensitive-cleanup and journal/report-write failures retain their existing fatal policy.

An administrator must confirm no active domain worker remains before inspecting access/ownership or removing the operation's leftover credential file. Preserve the result, journal and logs for investigation. Do not delete a live worker's input or restart a mutation manually to clear the warning. Failed staging can retain non-runnable ownership records when rollback cannot delete the credential; it must not be promoted into runnable work.

See [credential lifetime](../reference/security-and-credentials.md#domain-credentials) for memory and target-file boundaries. Verify joining, placement, local membership and credential disposal before organizational handoff.
