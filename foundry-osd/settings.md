# Settings

Foundry OSD settings mostly control the desktop application. The telemetry and remote-diagnostics toggles also affect the runtime configuration written during media creation. Proxy settings are limited to the Foundry OSD application.

## General

Configure:

- Whether Foundry OSD starts automatically with Windows.
- The application display language.
- Access to the application log directory.

Changing application language updates the authoring interface. It does not select the Windows PE or deployed Windows language.

### Export diagnostics

Use **Export diagnostics** to create a sanitized support archive. Sanitization is applied to the exported copy and does not modify the original application logs.

Use **Advanced: export raw logs** only when explicitly requested by a trusted support contact. Raw logs can contain credentials, identifiers, file paths, network names, and other sensitive data. Review the destination and handle the archive according to the organization’s security policy.

## Proxy

Use **Proxy** when Foundry OSD must connect to online services through an organization-managed proxy.

{% hint style="info" %}
These settings apply only to the Foundry OSD desktop application. They are not added to ISO or USB media and do not configure Foundry Connect or Foundry Deploy.
{% endhint %}

Choose a connection method:

- **Use Windows settings** uses the proxy configuration available to Windows and is recommended when the workstation is already managed by the organization.
- **Manual proxy** uses the server address, port, and bypass rules entered in Foundry OSD.
- **No proxy** connects directly and ignores the Windows proxy configuration.

For a manual proxy, enter an `http://` or `https://` server address and a port from 1 to 65535. Enable local-address bypass when host names without a dot should connect directly. Add optional host names or wildcard patterns to the bypass list, separated with semicolons.

Choose how Foundry OSD authenticates to a manual proxy:

- **Use current Windows credentials** uses the identity of the signed-in Windows user.
- **No authentication** sends no proxy credentials.
- **Username and password** uses the supplied username, password, and optional domain.

Explicit credentials are stored in Windows Credential Manager and are not written to the Foundry settings file. Applying a method that does not use explicit credentials removes the previously stored proxy credential.

Select **Apply** to save the settings and use them for subsequent connection and authentication requests, including requests made by existing Foundry OSD clients. Existing authenticated connections and authentication exchanges already in progress are not interrupted. Select **Test connection** to test the values currently displayed without saving or applying them. The test checks connectivity to GitHub, Microsoft sign-in, and Microsoft Graph endpoints; it does not verify account permissions or access to every download.

{% hint style="warning" %}
A successful test confirms only those Foundry OSD service checks. It does not validate connectivity from Windows PE, Foundry Connect, Foundry Deploy, or the installed Windows system.
{% endhint %}

## Telemetry

The two reporting settings are independent and enabled by default:

| Setting | When enabled |
| --- | --- |
| **Enable telemetry** | Sends anonymous usage and workflow events to PostHog Product Analytics. |
| **Enable remote diagnostics** | Sends application logs to PostHog Logs and separate exception reports to Error Tracking. |

Local application log files remain enabled when both settings are disabled.

- Both toggles apply to the Foundry OSD desktop application.
- Both preferences are synchronized into the runtime configuration used when media is created.

Restart Foundry OSD after changing **Enable telemetry** so desktop reporting uses the saved preference. Remote-diagnostics changes apply immediately to new records. Recreate media to apply either preference to Foundry Bootstrap, Foundry Connect, and Foundry Deploy; existing media is unchanged.

Telemetry excludes names, secrets, SSIDs, IP addresses, file paths, disk identifiers, computer names, Autopilot profile names, serial numbers, and hardware hashes. Deployment telemetry can include the device vendor and model. Events use an anonymous identifier created for the Foundry installation.

When telemetry is enabled, Foundry OSD can report the selected proxy method and, for a manual proxy, the authentication mode. It does not report the proxy address, port, bypass list, username, domain, password, PAC details, credentials, or tested URLs.

{% hint style="info" %}
**Unreleased: unified application logging**

The upcoming release sends every emitted application log level: Trace, Debug, Info, Warn, Error, and Fatal. The developer diagnostics card is removed: logging levels and warnings about missing translations no longer require a separate switch. The supported release still sends a filtered and more broadly sanitized selection of logs.
{% endhint %}

In the upcoming release, local and remote application logs share the original message, structured properties, exception details, and event timestamp after targeted masking of recognized authentication secrets. Operational identifiers, paths, network information, and tenant context can remain in Logs. Error Tracking keeps its separate, more restrictive sanitization and duplicate suppression.

The upcoming shared delivery mechanism queues logs on writable storage and retries transient failures. Reboot recovery requires persistent storage. Disabling remote diagnostics invalidates unsent records and attempts to delete their files; filesystem failures can prevent physical deletion. A request already in flight can complete. Re-enabling the setting does not upload local files recorded while it was disabled.

**Foundry's PostHog logs are retained for 7 days, then automatically deleted.** This does not change local-file retention. Pending delivery records have their own storage limits, and remote delivery can still have gaps or duplicates.

See [Telemetry and privacy](../reference/telemetry-and-privacy.md) for the complete data and delivery boundaries.

## Theme

Choose:

- Light, dark, or system-default application theme.
- Mica, Mica Alt, Acrylic, or Acrylic Thin backdrop.
- Windows accent-color settings.

Theme changes affect only the Foundry OSD authoring interface and do not alter deployment media.

## App updates

Review:

- Installed and available versions.
- Last update-check time.
- Configured update source.
- Release notes.

Use **Check for updates** to refresh status. When an update is available, review the release notes, then use the download and restart action. Complete the update before creating media when the release contains required compatibility or deployment fixes.

<figure>
  <img src="../.gitbook/assets/foundry-osd-settings-01-app-updates.png" alt="Foundry OSD application update status and actions">
  <figcaption>Review the installed version, update status, and available update actions.</figcaption>
</figure>
