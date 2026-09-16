# Network configuration

Configure network settings that Foundry Connect can use in Windows PE.

## Choose a connection type

- Use [Wi-Fi](wifi.md) when target devices must connect wirelessly.
- Use [Ethernet 802.1X](ethernet-802.1x.md) when wired access requires enterprise authentication.
- Skip network provisioning when target devices use an unrestricted wired connection with DHCP.

Only include profiles and certificates required for deployment. [Protected deployment](../general.md#protected-deployment) does not protect embedded network credentials: anyone who can read the ISO or USB can recover them. Restrict media access and use dedicated network credentials that can be revoked. Foundry Connect uses these credentials before the technician enters the deployment password, so automatic network setup remains available.

Use [deployment profiles](../deployment-profiles.md) to retain network settings and explicitly selected passwords or files, or to transfer them in an encrypted package. Review missing credentials and local certificate paths after activating or importing a profile.

## Windows profile roaming

Foundry can import selected Wi-Fi and wired 802.1X profiles into the installed Windows system before OOBE. Configure roaming separately for each connection type.

When certificate authentication is configured, **Include private-key certificate material** also copies the corresponding PFX client certificate into the Windows local-computer personal certificate store. Enable this only when Windows must continue using that certificate after deployment, and apply the organization’s rotation and revocation policy.

Profile roaming can also preserve a Wi-Fi profile created from a passphrase entered by the technician in Foundry Connect. Treat the resulting Windows network profile as sensitive because it contains the information required to reconnect.

## Before creating media

Confirm that:

- Required Windows PE network drivers are available.
- The deployment network can reach Windows, driver, and required Microsoft service endpoints.
- Certificates are valid for the deployment period.
- Test credentials and production credentials are not mixed.
- Profile and private-key roaming choices match the post-deployment connectivity requirement.
