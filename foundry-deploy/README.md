# Foundry Deploy

Foundry Deploy runs in Windows PE after network readiness. It guides a technician through target selection and Windows deployment.

## Before starting

- Confirm that the device is connected to power.
- Confirm that required network services are reachable.
- Disconnect storage devices that must not be selected accidentally.
- Obtain the technician password when Protected deployment is enabled.

{% hint style="danger" %}
Deployment erases and repartitions the selected target disk. Verify the target before starting.
{% endhint %}

When Protected deployment is enabled, Foundry Deploy requests the technician password before initialization. Cancelling the prompt closes Foundry Deploy. Incorrect attempts can be retried, with a progressively longer delay of up to five seconds. If the password is lost, recreate the media in Foundry OSD.

{% hint style="danger" %}
Foundry Deploy does not validate HTTPS server certificates for catalog and artifact downloads in Windows PE. Use deployment media only on a trusted, controlled network. A catalog-provided file hash can detect a changed download when a hash is available, but it is not an independent authenticity guarantee because the catalog is obtained through the same network path.
{% endhint %}

## Wizard sequence

1. [Select the target](target.md).
2. [Select Windows](operating-system.md).
3. [Select a driver pack](driver-pack.md).
4. Configure Windows Autopilot when JSON profile or zero-touch upload media requires a deployment-time choice. Interactive upload runs later during Windows OOBE and does not add this wizard step.
5. [Review and deploy](review-and-deploy.md).
6. [Verify deployment](verify-deployment.md).
