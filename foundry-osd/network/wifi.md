# Wi-Fi

Use the Wi-Fi page to prepare wireless connectivity for Foundry Connect.

## Configure a profile

1. Open **Network > Wi-Fi**.
2. Enable Wi-Fi provisioning.
3. Add or select the wireless profile required by the deployment environment.
4. Configure the authentication information requested by Foundry OSD.
5. Add a trusted root certificate when the enterprise network requires one.
6. Review the profile and return to **Start** to confirm readiness.

<!-- SCREENSHOT_PENDING: foundry-osd-network-wifi-01-profile-configuration.png | Show the Wi-Fi profile configuration controls with sanitized demonstration values. -->

{% hint style="warning" %}
Wireless profiles can contain sensitive configuration. Restrict access to generated media and never use real secrets in documentation screenshots.
{% endhint %}

## Provisioned profiles

Foundry Connect can display and use profiles included during media creation. Availability still depends on a supported wireless adapter and Windows PE driver.

## Validate before deployment

Test the media on representative hardware. Confirm that Foundry Connect can see the network, authenticate, obtain an IPv4 address, resolve required services, and report network readiness.
