# Interactive Domain Join

**Interactive Domain Join** keeps the join account off the deployment media: the technician types the account and its password in Foundry Deploy at each deployment.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-domain-join-interactive-01-configuration.png" alt="Interactive Domain Join page listing three domains and the OUs of the selected domain">
  <figcaption>Interactive mode lists domains and OUs only. No account or password is stored.</figcaption>
</figure>

## Before you start

- Make sure technicians have a join account, written as `DOMAIN\user` or `user@domain`, and its password.
- Check the requirements common to both modes in [Domain Join](README.md).

This mode does not need Password protection.

## Configure

1. Open **Domain Join > Interactive** and select **Enable**. If another Domain Join or Windows Autopilot mode is active, confirm with **Change mode**.
2. Optional: under **Domains**, select **Add**, enter the **Domain name**, such as `corp.contoso.com`, and select **Add domain**. Once a domain is listed, technicians can join only a listed domain; with none, they type the domain name.
3. Optional: select a domain, then under **Organizational units** select **Add**, enter a **Display name** and the **Distinguished name** of the OU, such as `OU=Workstations,DC=corp,DC=contoso,DC=com`, and select **Add OU**. **Import from domain** reads the OUs from the directory instead.
4. Open **Start** and create or update the media.

## What the technician sees

At each deployment, the [Domain Join step](../../foundry-deploy/domain-join.md) of Foundry Deploy asks for the domain, the join account, its password and, when the domain lists several, the OU. For a domain without listed OUs, the technician may type an OU or leave it empty.

Foundry Deploy does not contact the domain, so a mistyped password only shows after the restart, as a failed join.

## Check the result

- On **Start**, the Domain Join entry reads **Configured**.
- On a deployed device, follow the hand-over checks of the [Domain Join step](../../foundry-deploy/domain-join.md).

## Limits

No join account or password is written to interactive media, including the ones saved on the Zero-touch Domain Join page.

## Related

- [Domains and OUs](domains-and-ous.md)
- [Domain Join troubleshooting](../../troubleshooting/domain-join.md)
