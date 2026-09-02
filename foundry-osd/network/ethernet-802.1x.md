# Ethernet 802.1X

Use Ethernet 802.1X configuration when the wired deployment network requires authenticated access.

## Configure wired authentication

1. Open **Network > Ethernet 802.1X**.
2. Enable wired 802.1X provisioning.
3. Select or configure the required authentication profile.
4. Add the trusted root CA certificate required to validate the authentication service.
5. Choose whether the wired 802.1X profile should roam into Windows before OOBE.
6. When certificate authentication is used, choose whether the PFX client certificate and its private key must also be copied into Windows.
7. Review the configuration and return to **Start**.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-network-ethernet-01-profile-configuration.png" alt="Foundry OSD Ethernet 802.1X profile configuration">
  <figcaption>Configure the wired authentication profile and trusted root certificate.</figcaption>
</figure>

## Windows profile roaming

Enable **Roam network profiles to Windows** when the installed system must retain the wired 802.1X connection before OOBE. Foundry Deploy imports the profile into Windows during deployment.

For certificate-based profiles, **Include private-key certificate material** copies the configured PFX client certificate into the local-computer personal certificate store. Leave this disabled unless Windows requires the client certificate after WinPE, and apply the organization’s certificate rotation and revocation policy.

## Validate the deployment path

Test with the switch port, VLAN, certificate chain, and target hardware used in production. A successful cable link does not guarantee authenticated network access.

If Foundry Connect remains at authentication or DHCP readiness, see [Network and Foundry Connect troubleshooting](../../troubleshooting/network.md).
