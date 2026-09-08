# Network and Foundry Connect troubleshooting

## No adapter detected

**Likely causes:** unsupported hardware, missing Windows PE driver, disabled adapter, or device firmware setting.

**Actions:** verify firmware state, test another supported adapter, and rebuild media with the required WinPE driver.

## Ethernet link is unavailable

Check the cable, switch port, link status, and 802.1X requirements. A physical link can exist before authentication succeeds.

## Waiting for DHCP

Confirm that the deployment VLAN provides DHCP and that authentication or network-access control completed. Record the adapter, link, IPv4, and gateway status displayed by Foundry Connect.

## Wi-Fi network is not visible

Refresh networks, confirm the SSID is in range, and verify that the wireless adapter and Windows PE driver support the network.

## Wi-Fi authentication fails

Verify the passphrase or provisioned profile, enterprise certificate chain, time-sensitive certificate validity, and authentication method.

## Network is connected but deployment cannot continue

The local connection may be ready while DNS, proxy policy, or required Internet endpoints remain unavailable. Test the same network path with the organization’s approved diagnostic process.

## Foundry OSD cannot connect through a proxy

In **Settings > Proxy**, verify the connection method, address, port, bypass rules, and authentication mode, then select **Test connection**. The test uses the displayed values without saving them. If credentials have changed, enter the new credentials and select **Apply** to use them for subsequent authentication requests. Existing authenticated connections are not interrupted.

Foundry OSD proxy settings are not copied to boot media and do not configure Foundry Connect or Foundry Deploy. A successful desktop test does not confirm Windows PE connectivity. See [Proxy settings](../foundry-osd/settings.md#proxy).
