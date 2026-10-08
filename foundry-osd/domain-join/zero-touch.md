# Zero-touch Domain Join

Choose this mode when the media should carry the join account, so technicians do not type domain credentials. The credentials are protected by the technician password of [Password protection](../general.md#password-protection).

## Prepare the media

1. In [General configuration](../general.md#password-protection), turn on **Password protection** and enter the **Deployment password**. Zero-touch Domain Join cannot be used without it.
2. Open **Domain Join > Zero-Touch** and choose **Enable**. Confirm the replacement if another Domain Join or Autopilot mode is active.
3. Under **Shared join account**, enter the **Account**, as `DOMAIN\user` or `user@domain`, and its **Password**. Every domain uses this account unless you give it its own. Use an account provided by your Active Directory administrator, such as `djoin@corp.contoso.com`.
4. Under **Domains**, [add each domain](README.md#list-the-domains). To join a domain with a different account, choose **Use a dedicated account** in the domain dialog and enter that account and its password.
5. Optionally [add or import OUs](README.md#list-the-ous-of-a-domain) for each domain.
6. Resolve the messages shown on the page, then [create or update the media](../media/README.md).

The **Status** column of the Domains table says what each domain still needs: **Ready**, or for example a missing password. Problems with the shared account are shown under that account.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-domain-join-zero-touch-01-readiness.png" alt="Zero-touch Domain Join page with a shared join account, three domains marked Ready and the OUs of the selected domain">
  <figcaption>Each domain uses the shared account or its own, and shows Ready when nothing is missing.</figcaption>
</figure>

## What the technician sees

During deployment the technician unlocks the media with the technician password, as for any protected media. There is no prompt for the domain account or its password.

| Media | Domain join step in the Deploy wizard |
| --- | --- |
| One domain, and at most one OU for it | Not shown. The deployment asks nothing about the domain. |
| Several domains | Shown, to choose the domain. The default is preselected. |
| Several OUs for the domain being joined | Shown, to choose the OU. The default, if you set one, is preselected. |

See [Domain Join during deployment](../../foundry-deploy/domain-join.md).

## Passwords and profiles

A password belongs to its account, whether shared or dedicated:

- Changing an account, or removing the last domain that uses a dedicated account, clears its password.
- Renaming a domain, switching to Interactive and choosing **Disable** keep the passwords, so returning to Zero-touch needs no retyping. Kept passwords are only ever written to Zero-touch media.

On the media, each domain has its own encrypted copy of its account and password. A copy written for one domain cannot be used to join another.

With [deployment profiles](../deployment-profiles.md#domain-credentials):

- **Remember passwords** keeps the join passwords with the profile on this PC.
- A `.foundryprofile` export never contains the join passwords, even with **Include passwords and confidential files** selected. After importing such a profile on another PC, enter the join passwords and set the technician password there before creating media.
- An encrypted shared revision can contain the join passwords when you choose to include confidential content. Restrict access to its connection and recovery files and to their passwords accordingly.

Create or update the media again after changing an account or a password. Media already created keeps the credentials it was built with.

## Check the result

The join runs after Windows is installed and needs the domain to be reachable at that moment. Follow [Domain Join during deployment](../../foundry-deploy/domain-join.md#what-happens-in-windows) to check that the computer joined the domain and reached the intended OU.

During deployment, the credentials of the domain being joined are copied to the target disk, in a folder that only SYSTEM and administrators can read. They are used once in installed Windows and then deleted. See [how domain credentials are handled](../../reference/security-and-credentials.md#domain-credentials).
