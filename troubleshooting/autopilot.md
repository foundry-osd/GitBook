# Windows Autopilot troubleshooting

Identify the selected Autopilot method before troubleshooting.

## JSON profile is rejected

- Confirm that the file is valid JSON and is an approved Autopilot profile.
- Import the file again and review the parsed profile information.
- Recreate or update media after replacing the selected profile.

## Zero-touch readiness fails

Check tenant and application identifiers, Microsoft Graph application permissions, administrator consent, service-principal availability, certificate format, and certificate expiration.

## Group tags are missing in Foundry Deploy

For zero-touch upload, Foundry limits how long it waits for the available group-tag list at startup. If the lookup fails or times out, Foundry Deploy still opens and keeps the configured default tag available. Check the selected tag in the deployment summary.

If you need a tag that is missing, restore Microsoft service connectivity and restart Foundry Deploy before starting deployment. Review the [deployment logs](logs-and-support.md) for a group-tag discovery warning. Check network access, system time, certificate validity, application permission, and tenant policy. Successful startup does not confirm that hardware hash upload or registration checks can reach Microsoft services.

## Zero-touch upload fails during deployment

Confirm network access, system time, certificate validity, application permission, tenant policy, and whether the device is already registered.

If the motherboard was replaced, remove the old Windows Autopilot registration before retrying. Foundry captures and uploads the repaired device's current hardware hash, but an existing registration with the same serial number keeps its stored hash. Follow the [Microsoft motherboard replacement procedure](https://learn.microsoft.com/en-us/autopilot/autopilot-motherboard-replacement), then run the deployment again and verify the new registration before continuing to OOBE.

## Interactive sign-in fails

Confirm that the device code is entered before expiration, the technician uses an authorized account, delegated permissions are granted, and conditional-access policy permits the workflow.

## Upload succeeds but the device is not ready

Verify the device record, group tag, assigned profile, and assignment state in the organization’s management service before rebooting into OOBE.

## The computer name is not assigned

Confirm that [Upload computer name to Autopilot](../foundry-osd/customization/machine-naming.md#upload-the-computer-name-to-autopilot) is enabled on the Foundry OSD **Machine naming** page and that the deployment media includes the updated setting. Machine naming and a hardware hash upload method must both be enabled. The assigned value comes from the final name confirmed in Foundry Deploy, including operator edits.

Name assignment runs after the device becomes visible in Autopilot. For interactive upload, this happens in the OOBE assistant after technician sign-in. A custom answer file causes Foundry to skip name assignment because it does not manage that file's computer name.

Review the Autopilot result and [logs](logs-and-support.md) for an assignment failure or skip reason. A successful hardware hash upload does not prove the name was assigned. Check Microsoft Graph connectivity and permissions for the selected upload method. Autopilot assigned names apply to Microsoft Entra join, not hybrid join.

Never publish tenant identifiers, device codes, tokens, certificates, or hardware hashes in a support request.
