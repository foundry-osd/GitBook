# Telemetry and privacy

Foundry telemetry is intended to describe application and deployment behavior without collecting deployment secrets.

## Privacy expectations

Diagnostic and telemetry data must not include:

- Passwords or network secrets.
- Tokens, private keys, or certificate contents.
- Full hardware hashes.
- Sensitive query strings.
- User content unrelated to the deployment workflow.

Review the telemetry settings available in the current Foundry OSD release and apply organizational policy before deployment.

## Support attachments

Telemetry privacy rules do not automatically sanitize every file a user may attach to an issue. Review logs and screenshots manually before sharing them.
