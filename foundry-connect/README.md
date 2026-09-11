# Foundry Connect

Foundry Connect runs in Windows PE before deployment. It reports network state and allows the technician to establish connectivity required by Foundry Deploy.

## Runtime sequence

1. Boot the target device from Foundry deployment media.
2. Wait for Windows PE and Foundry Connect to initialize.
3. Review Ethernet status.
4. Select and connect to Wi-Fi when Wi-Fi provisioning was enabled during media creation and wired access is unavailable.
5. Continue after Foundry reports network readiness.

Foundry Connect may continue automatically after readiness is established. The interface shows the connection state, configuration source, refresh timing, and latest update.

After Foundry Connect completes, the bootstrap applies the [Windows PE time zone](../foundry-osd/general.md#windows-pe-time-zone). It uses the manual choice from the media configuration, or detects the time zone from the network's public IP address when **Automatic** is selected. Automatic detection falls back to **UTC** when no supported time zone can be resolved.

{% hint style="info" %}
**Clock synchronization**

Bootstrap attempts clock synchronization before Connect, with a two-second total budget. Connect still starts if networking is unavailable. Bootstrap retries after Connect establishes readiness if the clock remains unverified. Connect logs captured before synchronization keep their raw original time and appear in PostHog at server receipt time. See [Bootstrap startup](../reference/bootstrap.md#startup-progress) and [log timestamp handling](../reference/telemetry-and-privacy.md#application-logs).
{% endhint %}

Readiness confirms that an active network path and at least one configured connectivity probe succeeded. It does not verify every catalog, download, Microsoft, or organization-specific endpoint required later by Foundry Deploy.

{% hint style="warning" %}
Closing Foundry Connect aborts the bootstrap workflow. It does not bypass network readiness or continue to Foundry Deploy.
{% endhint %}

See [Network readiness](network-readiness.md) for status details.
