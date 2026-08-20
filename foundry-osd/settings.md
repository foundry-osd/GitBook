# Settings

Foundry OSD settings mostly control the desktop application. The telemetry toggle also affects the runtime telemetry configuration written during media creation.

## General

Configure:

- Whether Foundry OSD starts automatically with Windows.
- The application display language.
- Developer diagnostics.
- Access to the application log directory.

Changing application language updates the authoring interface. It does not select the Windows PE or deployed Windows language.

## Telemetry

Use **Enable telemetry** to control anonymous product telemetry.

- The toggle applies to the Foundry OSD desktop application.
- The same setting is synchronized into the runtime telemetry configuration used when media is created.

Telemetry excludes names, secrets, SSIDs, IP addresses, file paths, disk identifiers, computer names, Autopilot profile names, serial numbers, and hardware hashes. Deployment telemetry can include the device vendor and model. Events use an anonymous identifier created for the Foundry installation.

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
