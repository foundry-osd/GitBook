# Wi-Fi

Use the Wi-Fi page to prepare wireless connectivity for Foundry Connect.

## Configure a profile

1. Open **Network > Wi-Fi**.
2. Enable Wi-Fi provisioning.
3. Add or select the wireless profile required by the deployment environment.
4. Configure the authentication information requested by Foundry OSD.
5. Add a trusted root certificate when the enterprise network requires one.
6. Review the profile and return to **Start** to confirm readiness.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-network-wifi-01-profile-configuration.png" alt="Foundry OSD Wi-Fi profile configuration">
  <figcaption>Configure the wireless profile and any required trusted root certificate.</figcaption>
</figure>

{% hint style="warning" %}
Wireless profiles can contain sensitive configuration. Restrict access to generated media and never use real credentials or secrets in screenshots.
{% endhint %}

## Provisioned profiles

Foundry Connect can display and use profiles included during media creation. When Wi-Fi provisioning is enabled, Foundry automatically switches to the Wi-Fi-capable boot-image path, stages the required Wi-Fi dependencies, and injects the resolved WinPE driver packages during media creation.

Availability still depends on a supported wireless adapter and any additional vendor or custom WinPE drivers required by the target hardware.

## Validate before deployment

Test the media on representative hardware. Confirm that Foundry Connect can see the network, authenticate, obtain an IPv4 address, resolve required services, and report network readiness.
