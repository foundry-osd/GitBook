# Foundry Deploy

Foundry Deploy runs in Windows PE after network readiness. It guides a technician through target selection and Windows deployment.

## Before starting

- Confirm that the device is connected to power.
- Confirm that required network services are reachable.
- Disconnect storage devices that must not be selected accidentally.
- Obtain the technician password when protected deployment is enabled.

{% hint style="danger" %}
Deployment erases and repartitions the selected target disk. Verify the target before starting.
{% endhint %}

## Wizard sequence

1. [Select the target](target.md).
2. [Select Windows](operating-system.md).
3. [Select a driver pack](driver-pack.md).
4. [Review and deploy](review-and-deploy.md).
5. [Verify deployment](verify-deployment.md).
