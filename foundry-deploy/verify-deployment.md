# Verify deployment

Foundry Deploy finishes with a success or error state.

## Success

Review the completion message and session summary when the configured reboot policy allows it. Foundry OSD can configure a manual reboot, an immediate automatic reboot, or an automatic reboot after a displayed countdown. Configure a manual reboot or sufficient delay before creating media when technicians must inspect results or collect logs.

<figure>
  <img src="../.gitbook/assets/foundry-deploy-verify-01-success.png" alt="Foundry Deploy success page with the reboot action">
  <figcaption>Confirm deployment success before rebooting the device.</figcaption>
</figure>

After reboot:

- Confirm that Windows starts from the target disk.
- Confirm the expected computer name.
- When local accounts are configured, verify the expected account type and password sign-in, including built-in Administrator activation if enabled.
- Confirm network and required device drivers.
- Verify Windows Autopilot registration or staged profile when configured.
- Complete the organization’s acceptance checks before handoff.

When using a [custom answer file](../foundry-osd/customization/unattend.md), verify that its settings and commands produced the intended Windows configuration and work with the other enabled Foundry options. Foundry success confirms deployment completed; it does not prove that Windows has consumed every setting. Keep the target Panther answer file until the required setup passes finish, then follow your sensitive-file cleanup process.

{% hint style="info" %}
**Review step outcomes**

Review informational **Skipped** entries and their reasons as well as successful entries. A cache-related skip is expected reuse, but another skip can explain why requested optional work was not completed.

**Stage driver installer**, **Stage firmware update**, **Prepare setup tasks**, and **Prepare Autopilot assistant** describe preparation. Confirm the corresponding driver, firmware, customization, or registration result after Windows starts. A successful Deploy session does not establish that those later actions succeeded.
{% endhint %}

## Automatic Windows activation

For standard RETAIL deployments of supported Windows Home and Pro editions, Foundry automatically attempts to activate Windows after reboot using a compatible OEM product key stored in the device firmware. Online activation requires Internet access and a valid key for the installed Windows edition.

Foundry preserves existing activation and explicitly configured licensing. It skips this attempt for VOLUME deployments and deployments using a custom answer file. An unsuccessful activation attempt does not stop Windows setup. Before handing over the device, check **Settings > System > Activation** to confirm its activation status.

## Error

The error page identifies the failed deployment step and displays details.

<figure>
  <img src="../.gitbook/assets/foundry-deploy-verify-02-error.png" alt="Foundry Deploy error page showing the failed deployment step">
  <figcaption>Record the failed step and error details before troubleshooting.</figcaption>
</figure>

Do not immediately retry a destructive deployment. Record the failed step and error, then collect [logs and support information](../troubleshooting/logs-and-support.md).

Foundry Deploy does not roll back or resume a failed deployment. A failure after disk preparation can leave the target partially deployed and unable to boot. After correcting the cause, retrying starts the destructive workflow from the beginning.
