# Network readiness

Foundry Connect validates that the deployment environment can use the network before Foundry Deploy continues.

## Ethernet

Review:

- Adapter detection.
- Cable and link state.
- IPv4 address.
- Default gateway.
- DHCP or static configuration state.

## Wi-Fi

When Wi-Fi is enabled, you can:

- Refresh visible networks.
- Select a network.
- Enter or reveal a passphrase when required.
- Use a provisioned profile.
- Connect or disconnect.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-connect-network-readiness-01-waiting.png`
- **Capture:** Show Foundry Connect waiting for network with Ethernet and Wi-Fi status visible.
{% endhint %}

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-connect-network-readiness-02-ready.png`
- **Capture:** Show the Network ready state and Continue action.
{% endhint %}

## Common waiting states

- No supported network adapter.
- Ethernet cable disconnected.
- Waiting for DHCP.
- No visible Wi-Fi networks.
- Incorrect wireless password or network settings.
- Provisioned profile unavailable.
- Network present but required services unreachable.

Use the available refresh or retry action after correcting the cause. See [Network and Foundry Connect troubleshooting](../troubleshooting/network.md) when readiness does not complete.

Do not close Foundry Connect as a workaround. Closing the application aborts the bootstrap workflow.
