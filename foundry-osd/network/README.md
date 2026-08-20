# Network configuration

Configure network settings that Foundry Connect can use in Windows PE.

## Choose a connection type

- Use [Wi-Fi](wifi.md) when target devices must connect wirelessly.
- Use [Ethernet 802.1X](ethernet-802.1x.md) when wired access requires enterprise authentication.
- Skip network provisioning when target devices use an unrestricted wired connection with DHCP.

Only include profiles and certificates required for deployment. Generated media may contain sensitive network configuration and must be protected accordingly.

## Before creating media

Confirm that:

- Required Windows PE network drivers are available.
- The deployment network can reach Windows, driver, and required Microsoft service endpoints.
- Certificates are valid for the deployment period.
- Test credentials and production credentials are not mixed.
