# Foundry Deploy

Foundry Deploy runs in Windows PE after network readiness. It guides a technician through target selection and Windows deployment.

## Before starting

- Confirm that the device is connected to power.
- Confirm that required network services are reachable.
- Disconnect storage devices that must not be selected accidentally.
- Obtain the technician password when Protected deployment is enabled.
- For [Domain Join](domain-join.md), obtain interactive join credentials when needed and arrange installed-Windows domain-controller access.

{% hint style="danger" %}
Deployment erases and repartitions the selected target disk. Verify the target before starting.
{% endhint %}

When Protected deployment is enabled, Foundry Deploy requests the technician password before initialization. Cancelling the prompt closes Foundry Deploy. Incorrect attempts can be retried, with a progressively longer delay of up to five seconds. If the password is lost, recreate the media in Foundry OSD.

Automatic Domain Join reuses this unlock session. Interactive joining introduces no media-password prerequisite.

{% hint style="info" %}
Foundry Deploy validates HTTPS server certificates. When the catalog supplies a file hash, Deploy checks downloaded files and rechecks cached files before reuse. For cached Windows images and OEM driver packs, **Checking cache...** shows the verification percentage and bytes processed. Allow extra time when using large files or slower USB drives. A cached file that fails verification is downloaded again with its own download progress; a replacement that also fails verification stops the affected step. See [download troubleshooting](../troubleshooting/deployment.md#download-fails).
{% endhint %}

Keep deployment media under your control. Packages without a catalog hash retain compatibility support, but their contents cannot be verified against a catalog hash.

## Wizard sequence

1. [Select the target](target.md).
2. [Select Windows](operating-system.md).
3. [Select a driver pack](driver-pack.md).
4. Configure Windows Autopilot when JSON profile or zero-touch upload media requires a deployment-time choice. Interactive upload runs later during Windows OOBE and does not add this wizard step.
5. Complete the [Domain join step](domain-join.md#complete-the-domain-join-step) when the wizard shows it. It appears before the summary on Domain Join media that needs credentials or an OU choice from the technician.
6. [Review and deploy](review-and-deploy.md).
7. [Verify deployment](verify-deployment.md).
