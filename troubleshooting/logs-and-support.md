# Logs and support information

Collect evidence before rebooting, recreating media, or starting another deployment.

## Foundry OSD diagnostic export

In **Settings > General**, use **Export diagnostics** to save a sanitized archive of the desktop application's logs. The export does not modify the source logs or collect logs from a separate Windows PE deployment.

Use **Advanced: export raw logs** only when requested by a trusted support contact, and review the sensitive-data warning before sharing the archive. See [Export diagnostics](../foundry-osd/settings.md#export-diagnostics).

Remote error diagnostics do not replace local logs or a support archive; delivery is best effort. See [Telemetry and privacy](../reference/telemetry-and-privacy.md#remote-error-diagnostics).

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
