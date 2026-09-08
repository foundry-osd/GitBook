# Telemetry and privacy

Foundry uses two independent data controls to understand application usage and diagnose operational failures without collecting deployment secrets. Both controls are enabled by default and can be disabled independently in **Settings**.

## Anonymous product telemetry

**Enable telemetry** controls anonymous product and workflow events from Foundry OSD. The same preference is written to newly created media for Foundry Connect and Foundry Deploy.

Restart Foundry OSD after changing this setting so desktop reporting uses the saved preference. Changing the setting does not update existing media.

Telemetry can include application and release information, an anonymous installation identifier, workflow outcomes, durations, stable failure categories, and the device vendor and model during deployment. It excludes names, secrets, network identifiers, file paths, disk identifiers, computer names, Autopilot profile names, serial numbers, and hardware hashes.

### Custom answer files

When telemetry is enabled, using [custom answer files](../foundry-osd/customization/unattend.md) can report:

- Whether the feature is enabled and how many files are configured for the media. The count is capped at 100, meaning 100 or more; disabled catalogs report zero files.
- Whether the deployment default uses Foundry settings or a custom file.
- Whether the technician actually used Foundry settings or a custom file for a deployment.

Usage information can be reported even when media creation fails. File names, display labels, identifiers, source paths, content hashes, XML, and credentials are excluded. Use **Settings > Enable telemetry** to control this collection.

## Remote error diagnostics

**Enable remote diagnostics** controls privacy-filtered operational logs and exception details sent to PostHog. This setting is separate from anonymous product telemetry, applies immediately to new diagnostic records, and is written to newly created media for Foundry Connect and Foundry Deploy.

Remote diagnostics include warning, error, and fatal events, plus information events explicitly marked as terminal workflow diagnostics. Approved fields can include application and release context, random session or operation identifiers, workflow stage, duration, retry count, stable failure categories, and process exit codes.

Before delivery, Foundry applies an explicit property allowlist and sanitizes message, exception, and stack-trace content. Paths, URLs and other URIs, credentials, tokens, network identifiers, machine names, user names, and similar direct identifiers are removed or replaced. Full commands, process output, local file locations, and support-bundle details remain in local logs and are not exported as remote diagnostic attributes.

Remote delivery is best effort. Foundry uses a bounded in-memory queue with rate limiting and duplicate exception suppression, without a persistent outbox. Records can be dropped if the queue is full, the process exits, the network is unavailable, or PostHog rejects the request. A diagnostic delivery failure does not replace or change the original operation result.

Local logs remain the authoritative diagnostic source. Disabling remote diagnostics stops new records from entering the queue and discards records still waiting in Foundry's queue. Records already handed to the delivery transport may still be sent.

## Privacy expectations

Diagnostic and telemetry data must not include:

- Passwords or network secrets.
- Tokens, private keys, or certificate contents.
- Full hardware hashes.
- Sensitive query strings.
- User content unrelated to the deployment workflow.

Review the telemetry settings available in the current Foundry OSD release and apply organizational policy before deployment.

PostHog receives the connection source IP as transport metadata during direct HTTPS delivery. Foundry does not add it to event attributes, and Error Tracking events disable GeoIP enrichment.

## Proxy settings

When telemetry is enabled, Foundry OSD can include the selected proxy method and manual-proxy authentication mode in anonymous application telemetry. Proxy addresses, ports, bypass rules, usernames, domains, passwords, PAC details, credentials, and tested URLs are excluded.

## Support attachments

Telemetry and remote-diagnostics privacy rules do not automatically sanitize every file a user may attach to an issue. Review logs and screenshots manually before sharing them.
