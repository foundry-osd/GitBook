# Logs and support information

Collect evidence before rebooting, recreating media, or starting another deployment.

## Foundry OSD diagnostic export

In **Settings > General**, use **Export diagnostics** to save a sanitized archive of the desktop application's logs. The export does not modify the source logs or collect logs from a separate Windows PE deployment.

Use **Advanced: export raw logs** only when requested by a trusted support contact, and review the sensitive-data warning before sharing the archive. See [Export diagnostics](../foundry-osd/settings.md#export-diagnostics).

Remote diagnostics supplement local logs and support archives. Keep local evidence when investigating a failure: remote delivery can have gaps, and external-tool log files are not automatically uploaded. See [Telemetry and privacy](../reference/telemetry-and-privacy.md#remote-error-diagnostics).

## Find application logs in PostHog

**Foundry's PostHog logs are retained for 7 days, then automatically deleted.** Preserve evidence needed for a longer investigation before it expires. This retention does not delete local files or set the retention of Error Tracking reports.

Check the installed application and media versions when comparing local and remote records.

1. Confirm **Enable remote diagnostics** was enabled in Foundry OSD or in the configuration used to create the affected media. **Enable telemetry** is not required for Logs.
2. In PostHog Logs, select the time range covering the incident and the application service: `foundry_bootstrap`, `foundry_connect`, `foundry_deploy`, or `foundry_osd`. For Windows PE logs created before clock synchronization, include the time when network access returned and PostHog received them.
3. Include Trace, Debug, Info, Warn, Error, and Fatal. A zero count can simply mean no events at that level match the selected range.
4. Use the diagnostic session and available operation context to follow the workflow. Bootstrap, Connect, and Deploy share a session for the same boot.
5. Match the event identifier and original timestamp with the local record. If `diagnostics.clock_synchronized` is `false`, `diagnostics.timestamp_source` is `ingestion`: PostHog uses server receipt time for indexing while `diagnostics.original_timestamp` preserves the raw creation time. The flag reflects capture time, even if the clock was corrected before upload. Synchronized events and Foundry OSD use their creation time, although PostHog can replace an indexed timestamp more than 24 hours from ingestion and preserve the submitted value in `$originalTimestamp`. Use the process sequence to resolve ordering within one process when the system clock was corrected. See [timestamp handling](../reference/telemetry-and-privacy.md#application-logs).

Repeated messages are expected and are not rate limited by their content. Retries can create duplicate records with the same stable event identifier if the server accepted a batch but its response was lost.

If records are missing, check consent, application version, network access, the selected time range, and local delivery-health warnings. Each queue is limited to 4,096 records or 50 MiB; overflow removes its oldest records. Reboot recovery requires storage that survives, such as the Foundry Cache volume. Startup failures before consent is readable and internal delivery-health warnings remain local. Enabling diagnostics later does not backfill local files recorded while diagnostics were disabled. See [Delivery and retention](../reference/telemetry-and-privacy.md#delivery-and-retention) for queue locations and storage limits.

## Information to record

- Foundry application and media version.
- Device manufacturer and model.
- Current application: Foundry OSD, Foundry Bootstrap, Foundry Connect, or Foundry Deploy.
- Diagnostic session ID, when available, to match Bootstrap, Connect, and Deploy logs from the same boot.
- Current or failed workflow stage.
- Complete error message.
- Network state and connection type.
- Selected Windows release, edition, language, and architecture.
- Selected driver pack.
- Autopilot method, without credentials or tenant secrets.

## Windows PE log location

Foundry Bootstrap, Foundry Connect, and Foundry Deploy initially write logs under:

```text
X:\Foundry\Logs
```

The active files are `FoundryBootstrap.log`, `FoundryConnect.log`, and `FoundryDeploy.log`. Collect any rotated files covering the failure as well. For a failure before the deployment wizard appears, start with the [bootstrap stage and outcome](../reference/bootstrap.md).

The Bootstrap log records runtime source and cache decisions, stage durations, accepted child startup acknowledgements, clock correction, and time-zone outcomes. Detailed diagnostics remain in the log while the console shows a compact progress summary.

`FoundryBootstrap.Launcher.log` records the Bootstrap launch attempt and process exit code. Collect it if no Bootstrap progress or application log appears.

When a **Foundry Cache** volume is available, the bootstrap attempts to copy session logs to `<cache-drive>:\Logs\<session-id>`. Copying is best effort, so check that the files are present. Deploy can write additional logs after the bootstrap has finished.

Supervised startup evidence is stored under `<cache-drive>:\Logs\<session-id>\Startup\<launch-id>` when a cache is available, or `X:\Foundry\Logs\<session-id>\Startup\<launch-id>` otherwise. Collect `status.json`, any remaining `startup-failure.json`, and `startup-terminated.txt` alongside the logs. A failure record can disappear after Bootstrap transfers it into its pending diagnostic journal; this does not confirm remote delivery. See [Startup confirmation](../reference/bootstrap.md#startup-confirmation).

`X:` is temporary Windows PE storage. Copy relevant logs to persistent storage before rebooting; files on `X:` do not survive a reboot.

## Applied Windows log location

After Foundry prepares the target Windows installation, deployment logs are rebound to:

```text
<target-drive>:\Windows\Temp\Foundry\Logs
```

After the deployed operating system starts, this is normally:

```text
C:\Windows\Temp\Foundry\Logs
```

Relevant subdirectories include:

```text
PreOobe
AutopilotHash
AutopilotRegistration
```

If the first-boot runner does not start, also collect:

```text
C:\Windows\Panther\UnattendGC\Setupact.log
```

{% hint style="warning" %}
Review collected files before sharing them. Remove credentials, tokens, certificates, hardware hashes, tenant identifiers, network secrets, and other sensitive information.
{% endhint %}

## Open a support issue

Provide reproduction steps, expected result, actual result, failed stage, sanitized logs, and whether the problem reproduces on newly created media.
