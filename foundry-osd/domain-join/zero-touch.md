# Zero Touch Domain Join

{% hint style="warning" %}
**Unreleased draft** — Requires a supporting Foundry release and native deployment acceptance. See [Domain Join prerequisites](README.md#before-configuring).
{% endhint %}

Choose this mode when protected deployment media should supply the join credentials. An optional destination picker can still require technician input.

## Prepare protected media

1. In [General configuration](../general.md#protected-deployment), enable **Protected deployment** and supply its existing media password.
2. Open **Domain Join > Zero Touch Domain Join** and choose **Activate**. Confirm replacement if another Domain Join or Autopilot mode is active.
3. Enter **Target DNS domain**, then **Account (DOMAIN\user or user@domain)** and **Account password** under **Domain credentials**. Use an administrator-provided account such as `CORP\deployment-join` for `corp.example.test`.
4. Optionally [add or import destinations](README.md#configure-destinations). Choose a default and enable **Allow destination selection during deployment** only if technician selection is wanted.
5. Resolve all readiness messages and [create or update media](../media/README.md).

The automatic password is encrypted using the existing Protected deployment key. Deploy uses the existing unlock session; there is no additional media password or domain-password prompt. Without a destination picker, no Domain join dialog is introduced. With a picker, the domain is read-only and the credential editors are hidden; the technician must choose a catalog row before continuing.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-domain-join-zero-touch-01-readiness.png`
- **Capture:** Show activated Zero Touch Domain Join, credential field labels without credentials, readiness and the optional destination policy.
{% endhint %}

## Keep credentials associated with the correct profile

Changing the domain, account or active mode clears the entered password. Re-enter it for the reviewed identity. **Deactivate** excludes joining from new media and clears domain credentials while retaining nonsecret settings.

[Remember passwords](../deployment-profiles.md#domain-credentials-unreleased) can retain the password with a local profile. Ordinary `.foundryprofile` exports always omit the direct domain password, even with **Include passwords and confidential files** selected. An imported automatic profile remains structurally valid, but media creation needs the password and usable General protection on that PC.

Encrypted shared revisions use a separate sharing path and may retain domain credentials when confidential inputs are explicitly included. Connection and recovery files omit the direct password, but their shared-access keys can grant access to secrets in those revisions. Restrict those files and their passwords accordingly.

Recreate or update media after credential changes. Profile edits do not change media already distributed or a build already running.

## Verify deployment

Follow [Domain Join in Deploy](../../foundry-deploy/domain-join.md). Installed Windows needs online domain-controller access even when media has an offline OU catalog. Review joining, placement and post-restart membership separately.

Credentials become a restricted temporary plaintext target payload for the child operation. See [credential lifetime and cleanup](../../reference/security-and-credentials.md#domain-credentials-unreleased); cleanup warnings require administrator review before handoff.
