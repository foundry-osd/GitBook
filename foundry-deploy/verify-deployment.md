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
- Confirm network and required device drivers.
- Verify Windows Autopilot registration or staged profile when configured.
- Complete the organization’s acceptance checks before handoff.

For the unreleased [custom answer-file feature](../foundry-osd/customization/unattend.md), also verify the expected time zone, OOBE, accounts, commands, and enrollment outcome. Foundry success confirms deployment completed; it does not prove that Windows has consumed every setting. Keep the target Panther answer file until the required setup passes finish, then follow your sensitive-file cleanup process.

## Error

The error page identifies the failed deployment step and displays details.

<figure>
  <img src="../.gitbook/assets/foundry-deploy-verify-02-error.png" alt="Foundry Deploy error page showing the failed deployment step">
  <figcaption>Record the failed step and error details before troubleshooting.</figcaption>
</figure>

Do not immediately retry a destructive deployment. Record the failed step and error, then collect [logs and support information](../troubleshooting/logs-and-support.md).

Foundry Deploy does not roll back or resume a failed deployment. A failure after disk preparation can leave the target partially deployed and unable to boot. After correcting the cause, retrying starts the destructive workflow from the beginning.
