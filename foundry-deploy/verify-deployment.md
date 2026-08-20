# Verify deployment

Foundry Deploy finishes with a success or error state.

## Success

Review the completion message and session summary. Use the reboot action or follow the displayed reboot instruction.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-deploy-verify-01-success.png`
- **Capture:** Show the deployment success page and reboot action.
{% endhint %}

After reboot:

- Confirm that Windows starts from the target disk.
- Confirm the expected computer name.
- Confirm network and required device drivers.
- Verify Windows Autopilot registration or staged profile when configured.
- Complete the organization’s acceptance checks before handoff.

## Error

The error page identifies the failed deployment step and displays details.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-deploy-verify-02-error.png`
- **Capture:** Show the deployment error page with a sanitized sample failure.
{% endhint %}

Do not immediately retry a destructive deployment. Record the failed step and error, then collect [logs and support information](../troubleshooting/logs-and-support.md).
