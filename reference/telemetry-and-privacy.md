# Telemetry and privacy

Foundry uses two independent data controls: **Enable telemetry** for PostHog Product Analytics, and **Enable remote diagnostics** for PostHog Logs and Error Tracking. Both controls are enabled by default and can be disabled independently in **Settings**. Local application logging remains enabled when both controls are disabled.

## Anonymous product telemetry

**Enable telemetry** controls anonymous product and workflow events from Foundry OSD. The same preference is written to newly created media for Foundry Bootstrap, Foundry Connect, and Foundry Deploy.

Restart Foundry OSD after changing this setting so desktop reporting uses the saved preference. Changing the setting does not update existing media.

Telemetry can include application and release information, an anonymous installation identifier, workflow outcomes, durations, stable failure categories, and the device vendor and model during deployment. It excludes names, secrets, network identifiers, file paths, disk identifiers, computer names, Autopilot profile names, serial numbers, and hardware hashes.

### Custom answer files

When telemetry is enabled, using [custom answer files](../foundry-osd/customization/unattend.md) can report:

- Whether the feature is enabled and how many files are configured for the media. The count is capped at 100, meaning 100 or more; disabled catalogs report zero files.
- Whether the deployment default uses Foundry settings or a custom file.
- Whether the technician actually used Foundry settings or a custom file for a deployment.

Usage information can be reported even when media creation fails. File names, display labels, identifiers, source paths, content hashes, XML, and credentials are excluded. Use **Settings > Enable telemetry** to control this collection.

## Remote error diagnostics

**Enable remote diagnostics** controls operational logs and exception reports sent to PostHog. This preference is separate from anonymous product telemetry, applies immediately to new diagnostic records, and is written to newly created media for Foundry Bootstrap, Foundry Connect, and Foundry Deploy. Changing it does not update existing media.

{% hint style="info" %}
**Unreleased: unified application logging**

The all-level logging and durable delivery behavior below belongs to the upcoming Foundry release. The supported release sends a filtered selection of operational logs with broader privacy sanitization; it does not provide the shared durable log outbox described here. Update the applications and [refresh boot media](supported-versions.md#application-and-boot-media-updates) when the release becomes available.
{% endhint %}

### Application logs

Foundry OSD, Bootstrap, Connect, and Deploy use one shared application logging pipeline. A normal log call writes to the local file and, when remote diagnostics are enabled, queues the same event for PostHog. No additional remote marker is required. PostHog receives emitted events at all six levels:

| Application level | PostHog level |
| --- | --- |
| Verbose / Trace | Trace |
| Debug | Debug |
| Information | Info |
| Warning | Warn |
| Error | Error |
| Fatal / Critical | Fatal |

Repeated messages are retained without per-message rate limiting. A level can still show zero results when the application did not emit an event at that level or the selected time range contains none.

The shared pipeline preserves the rendered message, message template, structured properties, and exception details, including stack traces. It masks recognized authentication secrets before writing either destination, rather than removing ordinary diagnostic properties. Paths, technical URLs, process output, machine names, network information, and tenant context can therefore appear when application code logs them. These logs are operational data, not anonymous product telemetry. Do not add credentials or unnecessary sensitive content to log messages; targeted masking cannot identify every arbitrary secret in free text.

Events retain their original timestamp, represented in UTC for transport, plus a stable event identifier and a process sequence. Delivery after reconnecting does not replace the event time with the upload time. Clock correction during boot can change wall-clock order; the process sequence preserves emission order within that process. It is not a shared counter across all applications.

PostHog can adjust the indexed timestamp after receiving a record. Its current ingestion code replaces timestamps more than 24 hours before or after ingestion with the ingestion time and keeps the submitted value in `$originalTimestamp`. Foundry still sends the original timestamp over OTLP. For a long offline period or an incorrect boot clock, inspect that attribute and the local log when comparing times. See [PostHog's log timestamp handling](https://github.com/PostHog/posthog/blob/master/rust/capture-logs/src/log_record.rs).

Use `service.name` to select an application:

| Application | Service |
| --- | --- |
| Foundry Bootstrap | `foundry_bootstrap` |
| Foundry Connect | `foundry_connect` |
| Foundry Deploy | `foundry_deploy` |
| Foundry OSD | `foundry_osd` |

Application version, component, and available session or operation context help correlate records. Bootstrap, Connect, and Deploy share a diagnostic session for the same boot; Foundry OSD has its own application session. Files written by external tools or the command launcher are not automatically uploaded by this application logging pipeline. Internal delivery failures stay local to avoid recursively logging failures through the failing transport. Startup failures before reporting consent can be read remain local.

### Delivery and retention

The shared log pipeline stores pending records before sending them asynchronously in batches over HTTPS using OpenTelemetry OTLP/Protobuf. It retires accepted batches after a successful server response and retries transient failures. Application execution and shutdown do not wait indefinitely for PostHog.

Pending records are isolated by installation and PostHog destination. Disabling remote diagnostics stops new remote capture, invalidates unsent records, and attempts to delete their files. Persisted revocation markers prevent revoked queues from being replayed, including when deleting individual payload files fails. Storage failures are reported locally and can leave files on disk; disabling the setting is not a guarantee of physical file erasure. It does not delete logs already accepted by PostHog, and a request already in flight can still complete. Re-enabling diagnostics does not upload local log-file history collected while the setting was disabled.

Each active or recovered log queue holds at most **4,096 records or 50 MiB**, whichever limit is reached first. If the queue exceeds a limit, it removes the oldest queued records and writes a local delivery-health warning with loss counts. A single record larger than the byte limit cannot be queued. These limits apply to each queue, not to the total storage used by every running application. Pending logs have no separate age-based expiry; PostHog's seven-day retention is independent and does not expire the local outbox.

Foundry OSD stores pending logs under `PendingLogs` beside its selected application log files. Bootstrap stores its persistent queue under `<cache-drive>:\Diagnostics\Bootstrap\pending.jsonl.logs`. Connect and Deploy use `<cache-drive>:\Logs\PendingLogs` when Bootstrap supplies a valid cache-backed session directory; otherwise their queue sits under `PendingLogs` beside the selected local log files. Subdirectories separate services, hashed destinations, and process writers. Queue files contain masked operational records and should receive the same protection as local logs.

Retry survives a process restart when the pending-record directory remains available and writable. Bootstrap uses bounded memory when no persistent cache is available. On Windows PE, data stored only on the `X:` RAM drive is lost on reboot. If storage fails, new records can remain only in bounded memory; a process exit can then lose them. Unavailable storage, exhausted capacity, or a rejected payload can leave gaps. The pipeline reports delivery losses locally. Preserve local logs when investigating a failure.

Server acceptance is not a guarantee that a record is already searchable. If a server accepts a batch but its response is lost, retry can produce duplicates. Stable event identifiers allow those duplicates to be recognized; delivery does not guarantee exactly one copy.

**Logs in Foundry's PostHog project are retained for 7 days, then automatically deleted.** This is the retention configured for this project, not a universal PostHog limit. It does not set the retention of local log files, pending delivery records, Product Analytics, or Error Tracking. Export any evidence needed for a longer investigation before it expires, following your organization's data-handling policy.

### Error Tracking

Error Tracking remains separate from the all-level Logs stream. Exception reports retain their conservative property allowlist, privacy sanitization, and duplicate suppression. Enabling Trace or recording repeated log entries does not create an equivalent number of Error Tracking reports. An exception can appear in both products, with more operational detail in Logs.

## Bootstrap reporting

Bootstrap creates one `bootstrap:failed` product event when startup ends in an unrecoverable failure. It creates no product event for successful startup, cancellation, or a recovered warning. Remote diagnostics remain independently controlled and can be enabled when product telemetry is disabled.

Bootstrap reads its preferences from `X:\Foundry\Config\foundry.bootstrap.config.json`. Missing or invalid Bootstrap configuration keeps reporting local. An explicit opt-out in either child application's configuration also disables the corresponding Bootstrap category. Bootstrap reads these preferences without decrypting deployment or network secrets.

Eligible records are captured before connectivity is available. Background delivery starts after Connect succeeds and system preparation has attempted clock synchronization. An earlier failure gets a bounded delivery attempt at shutdown. Reporting cannot prevent boot from continuing or change its result.

In the upcoming release, Bootstrap application logs use the shared [delivery and retention](#delivery-and-retention) mechanism. They keep their original timestamps even when captured before clock correction. Clock synchronization cannot retrospectively correct an inaccurate timestamp already recorded.

Product events and recovered Error Tracking reports keep their separate Bootstrap recovery journal under `<cache-drive>:\Diagnostics\Bootstrap` when a **Foundry Cache** volume is available. Their existing consent and recovery limits are independent of the shared log outbox. Without persistent storage, recovery cannot survive reboot. Do not treat a transferred startup failure record or a queued Error Tracking report as confirmation of PostHog log delivery.

For an early Connect or Deploy failure, the child application writes a bounded, sanitized failure record before publishing its failure status. For a reported startup failure, Bootstrap allows up to five seconds for child cleanup before its own reporting shutdown. It can recover the record after the child exits, preserving its original application, exception details, and identifiers. The child does not independently upload the same transferred record. If Bootstrap has already stopped, a later boot can recover it from persistent cache storage once the child is no longer running. Temporary Windows PE storage is lost on reboot.

Recovered child exceptions use Bootstrap's exception-recovery consent and destination rules. Bootstrap reports a process outcome when no child exception is available; an exit code or readiness timeout is not presented as an invented child exception. Errors after UI readiness remain owned by the child application.

## Privacy expectations

Application code must not deliberately log or report:

- Passwords or network secrets.
- Tokens, private keys, or certificate contents.
- Full hardware hashes.
- Sensitive query strings.
- User content unrelated to the deployment workflow.

Review the telemetry settings available in the installed Foundry OSD release and apply organizational policy before deployment. In the upcoming release, all-level Logs can contain operational identifiers that Product Analytics and Error Tracking exclude. Review access to the PostHog project accordingly.

PostHog receives the connection source IP as transport metadata during direct HTTPS delivery. Product Analytics does not add it to event attributes, and Error Tracking events disable GeoIP enrichment. Operational log messages can contain network addresses logged by application code.

## Proxy settings

When telemetry is enabled, Foundry OSD can include the selected proxy method and manual-proxy authentication mode in anonymous application telemetry. Proxy addresses, ports, bypass rules, usernames, domains, passwords, PAC details, credentials, and tested URLs are excluded.

## Support attachments

Telemetry and remote-diagnostics privacy rules do not automatically sanitize every file a user may attach to an issue. Review logs and screenshots manually before sharing them.
