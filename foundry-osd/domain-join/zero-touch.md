# Zero-touch Domain Join

Choose this mode when protected deployment media should supply the join credentials. Technician choice of the domain or the OU, if enabled, still requires input.

## Prepare protected media

1. In [General configuration](../general.md#protected-deployment), enable **Protected deployment** and supply its existing media password.
2. Open **Domain Join > Zero-Touch** and choose **Enable**. Confirm replacement if another Domain Join or Autopilot mode is active.
3. Under **Shared join account**, enter **Account (DOMAIN\user or user@domain)** and **Password**. Every domain uses this account unless you give it its own. Use an administrator-provided account such as `CORP\deployment-join`.
4. Under **Domains**, [add each domain](README.md#list-the-domains). To join a domain with a different account, choose **Use a dedicated account** in the domain dialog and enter that account and its password.
5. Optionally [add or import OUs](README.md#list-the-ous-of-a-domain) for each domain, and set a default OU.
6. Turn on **Technicians can choose** on a card only if technician selection of the domain or of the OU is wanted.
7. Resolve all readiness messages and [create or update media](../media/README.md).

The **Status** column of the Domains table says what each domain still needs: **Ready**, or for example a missing password. Problems with the shared account are shown under that account.

Each join password is encrypted using the existing Protected deployment key, once per domain, so a password written for one domain cannot be used for another. Deploy uses the existing unlock session; there is no additional media password or domain-password prompt. Without technician choice, the Deploy wizard shows no Domain join step and joins the default domain. With it, the step shows the domain and OU choices and no credential fields.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-domain-join-zero-touch-01-readiness.png`
- **Capture:** Show enabled Zero-touch Domain Join: the Shared join account card without credentials, the Domains table with its Status column, and the Organizational units card.
{% endhint %}

## Keep credentials associated with the correct profile

A password belongs to its account. Changing an account or removing the last domain that uses it clears its password; renaming a domain, switching to Interactive and choosing **Disable** do not, so returning to Zero-touch needs no retyping. Passwords kept this way are written only to Zero-touch media. **Disable** excludes joining from new media while retaining the settings and their passwords.

[Remember passwords](../deployment-profiles.md#domain-credentials) can retain the password of each join account with a local profile. Ordinary `.foundryprofile` exports always omit the direct domain password, even with **Include passwords and confidential files** selected. An imported automatic profile remains structurally valid, but media creation needs the password and usable General protection on that PC.

Encrypted shared revisions use a separate sharing path and may retain domain credentials when confidential inputs are explicitly included. Connection and recovery files omit the direct password, but their shared-access keys can grant access to secrets in those revisions. Restrict those files and their passwords accordingly.

Recreate or update media after credential changes. Profile edits do not change media already distributed or a build already running.

## Verify deployment

Follow [Domain Join in Deploy](../../foundry-deploy/domain-join.md). Installed Windows needs online domain-controller access even when the media carries an OU list. Review joining, placement and post-restart membership separately.

Credentials become a restricted temporary plaintext target payload for the child operation. See [credential lifetime and cleanup](../../reference/security-and-credentials.md#domain-credentials); cleanup warnings require administrator review before handoff.
