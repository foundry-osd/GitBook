# Network readiness

Foundry Connect confirms that the device has an active network path and that at least one configured connectivity probe returns a successful response before Foundry Deploy continues. This is a basic connectivity check, not validation of every service required during deployment.

<figure>
  <img src="../.gitbook/assets/foundry-connect-network-readiness-01-ready.png" alt="Foundry Connect showing network readiness, Ethernet details, and available Wi-Fi networks">
  <figcaption>Review the ready state and available network connections before continuing.</figcaption>
</figure>

## Ethernet

Review:

- Adapter detection.
- Cable and link state.
- IPv4 address.
- Default gateway.
- DHCP or static configuration state.

## Wi-Fi

Wi-Fi controls are available only when Wi-Fi provisioning was enabled while creating the media. You can then:

- Refresh visible networks.
- Select a network.
- Enter or reveal a passphrase when required.
- Use a provisioned profile.
- Connect or disconnect.

Manual connection is intended for open, OWE, and personal Wi-Fi networks. Enterprise Wi-Fi requires a compatible profile and certificate configuration provisioned by Foundry OSD.

{% hint style="warning" %}
When **Roam network profiles to Windows** was enabled during media creation, a compatible profile created from a passphrase entered here can be imported into Windows before OOBE. Use only credentials approved to persist on the deployed device.
{% endhint %}

## Common waiting states

- No supported network adapter.
- Ethernet cable disconnected.
- Waiting for DHCP.
- No visible Wi-Fi networks.
- Incorrect wireless password or network settings.
- Provisioned profile unavailable.
- Network present but no configured connectivity probe succeeds.

Use the available refresh or retry action after correcting the cause. See [Network and Foundry Connect troubleshooting](../troubleshooting/network.md) when readiness does not complete.

After readiness succeeds, required GitHub catalogs, content sources, and Microsoft services can still be blocked. Validate all endpoints required by the selected deployment and Autopilot workflow before distributing media.

Do not close Foundry Connect as a workaround. Closing the application aborts the bootstrap workflow.
