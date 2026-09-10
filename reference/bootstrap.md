# Windows PE bootstrap

Foundry Bootstrap prepares the Windows PE session, starts Foundry Connect, and launches Foundry Deploy after Connect succeeds. Use this page to follow startup progress or investigate a problem before the deployment wizard appears.

## Startup progress

The console shows five stages in order:

| Stage | What happens |
| --- | --- |
| Prepare environment | Selects the x64 or ARM64 runtime and storage location, then prepares wired authentication and supported wireless services. |
| Start Connect | Resolves Foundry Connect, starts it, and waits for the network workflow to finish. |
| Prepare system | Attempts to correct the clock and configure the time zone after Connect succeeds. |
| Prepare deployment application | Resolves Foundry Deploy. On release-provisioned USB media, also checks for a Connect runtime update. |
| Start Deploy | Launches Foundry Deploy. |

Text statuses identify pending, running, completed, warning, failed, and cancelled work. Downloads show transferred data and a percentage when the total size is available. Longer operations show elapsed time.

A warning describes a recoverable issue; startup can continue. A failure identifies the affected stage and displays the diagnostic session ID and log location.

The final success message confirms that Deploy was launched. Wait for the deployment wizard before proceeding. If it does not appear, collect both Bootstrap and Deploy logs.

Closing or cancelling Foundry Connect stops the boot workflow; it does not bypass network readiness. A Connect startup or configuration failure also prevents Deploy from launching. See [Network readiness](../foundry-connect/network-readiness.md) for the technician workflow.

## Cache and connectivity

A volume labelled **Foundry Cache** takes precedence for runtime storage. Otherwise, Bootstrap uses `X:\Foundry\Runtime` in temporary Windows PE storage.

Bootstrap tries to use an available Connect runtime before requiring a release lookup, so Connect can help establish networking. Application release lookup or download failures can fall back to usable cached content or an embedded archive when one is available. A fallback warning can therefore be followed by a successful launch.

A cache does not guarantee a fully offline deployment. Connect still requires its connectivity checks to succeed, and the selected Windows, drivers, catalogs, or Autopilot workflow may require additional services. If neither usable local content nor a downloadable runtime is available, boot stops before the affected application launches.

Debug-provisioned runtimes skip the normal release update lookup. Record whether the media uses release or debug content when reporting a startup problem.

## Collect startup evidence

Record the last console stage, the displayed result, and the diagnostic session ID. Bootstrap, Connect, and Deploy share that ID so their log entries can be matched across the same boot.

Start with `FoundryBootstrap.log`, then collect the affected application's log. See [Windows PE log location](../troubleshooting/logs-and-support.md#windows-pe-log-location) for filenames, cache copies, and the information to preserve before rebooting.

For diagnostic data settings, see [Telemetry and privacy](telemetry-and-privacy.md). For refreshing existing media, see [Application and boot media updates](supported-versions.md#application-and-boot-media-updates).
