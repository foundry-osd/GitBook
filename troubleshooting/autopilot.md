# Windows Autopilot troubleshooting

Identify the selected Autopilot method before troubleshooting.

## JSON profile is rejected

- Confirm that the file is valid JSON and is an approved Autopilot profile.
- Import the file again and review the parsed profile information.
- Recreate or update media after replacing the selected profile.

## Zero-touch readiness fails

Check tenant and application identifiers, Microsoft Graph application permissions, administrator consent, service-principal availability, certificate format, and certificate expiration.

## Zero-touch upload fails during deployment

Confirm network access, system time, certificate validity, application permission, tenant policy, and whether the device is already registered.

## Interactive sign-in fails

Confirm that the device code is entered before expiration, the technician uses an authorized account, delegated permissions are granted, and conditional-access policy permits the workflow.

## Upload succeeds but the device is not ready

Verify the device record, group tag, assigned profile, and assignment state in the organization’s management service before rebooting into OOBE.

Never publish tenant identifiers, device codes, tokens, certificates, or hardware hashes in a support request.
