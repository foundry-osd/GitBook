# Foundry Connect

Foundry Connect runs in Windows PE before deployment. It reports network state and allows the technician to establish connectivity required by Foundry Deploy.

## Runtime sequence

1. Boot the target device from Foundry deployment media.
2. Wait for Windows PE and Foundry Connect to initialize.
3. Review Ethernet status.
4. Select and connect to Wi-Fi when it is enabled and wired access is unavailable.
5. Continue after Foundry reports network readiness.

Foundry Connect may continue automatically after readiness is established. The interface shows the connection state, configuration source, refresh timing, and latest update.

{% hint style="warning" %}
Closing Foundry Connect aborts the bootstrap workflow. It does not bypass network readiness or continue to Foundry Deploy.
{% endhint %}

See [Network readiness](network-readiness.md) for status details.
