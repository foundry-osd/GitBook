# Telemetry and privacy

Foundry can send two kinds of data to its developers: anonymous usage events, and application logs with error reports. Each has its own switch in Foundry OSD, both are on by default, and you can turn either off.

## Summary

| | **Enable telemetry** | **Enable remote diagnostics** |
| --- | --- | --- |
| What is sent | Anonymous usage events | Application logs of all levels, and error reports |
| Sent by | Foundry OSD, and on the target device the Windows PE startup, Foundry Connect and Foundry Deploy | The same four |
| Destination | `https://eu.i.posthog.com` (PostHog, hosted in the European Union) | The same host |
| Kept for | Not stated by the application | Logs are deleted after 7 days |

Foundry's post-installation step, which runs after the restart, sends nothing.

Log files are always written locally, whatever the switches say. See [Log locations](../troubleshooting/logs-and-support.md#log-locations).

## What telemetry sends

Telemetry is a small number of events, each sent at a precise moment:

| Sent by | When | Content |
| --- | --- | --- |
| Foundry OSD | Once a day, when the app starts | Proxy method, and the authentication mode of a manual proxy |
| Foundry OSD | When a media creation ends, whether it succeeded, failed or was cancelled | Result, duration, failed step, and the options chosen for the media (listed below) |
| Windows PE startup | Only when the startup fails | Failure category, last stage reached, elapsed time, and which application failed to start |
| Foundry Connect | When the network is ready | Connection type (Ethernet or Wi-Fi), what was available, Wi-Fi security type, whether 802.1X or a Wi-Fi profile was on the media |
| Foundry Deploy | When a deployment ends | Result, duration, failed step; device manufacturer and model and whether it is a virtual machine; Windows release, build, architecture, language, edition and licensing; driver pack source, manufacturer and model; whether firmware updates were on; Windows Autopilot and Domain Join mode and state; restart setting |

Every event also carries the application name and version, its language and architecture, a session identifier, and an anonymous installation identifier created at random by Foundry OSD.

<details>

<summary>Options reported when a media creation ends</summary>

Values are switches, counts or fixed choices. No name, path or text you typed is included.

| Area | What is reported |
| --- | --- |
| Media | ISO or USB, creation or update; architecture; Windows PE language; standard or Wi-Fi boot image; Secure Boot signature; USB partition style and format mode; where the Foundry applications put on the media came from |
| Drivers | Whether Dell, HP or a custom driver folder is used |
| General | Whether Password protection is on; restart mode and delay; whether a Windows PE time zone is set |
| Network | Whether Ethernet 802.1X and Wi-Fi are on, whether a profile, a passphrase or a certificate is configured, the Wi-Fi security type, and whether profiles are kept for Windows |
| Windows Autopilot | Whether it is on, and the method |
| Domain Join | Whether it is on, the method, the number of domains (up to 32) and OUs, whether a shared join account and a default OU are set |
| OS selection | Number of allowed languages, releases, editions and licensing types, and whether a default is set for each |
| Custom Windows images | Whether they are on, how many are included (up to 100), and whether the default is a catalog or a custom image |
| Unattend | Whether it is on, the number of answer files (up to 100), and whether the default is Foundry's settings or a custom file |
| Machine naming | Whether it is on, the mode, the number and types of name components, separator, casing, truncation, and whether the technician may edit the name |
| OOBE | Whether it is on, and each privacy choice: license terms, diagnostic data level, privacy screen, tailored experiences, advertising ID, speech recognition, inking and typing, location |
| Post-installation | Whether it is on, the number of actions (up to 1,000), and the count of each type: PowerShell script, command, software installation, restart |
| Optional features | Whether it is on, the number of features to enable and to disable, and whether a source folder is needed |
| AppX removals | Whether it is on, the number of apps and the list preset used |
| AI components | Whether it is on, and each option chosen |

</details>

## What remote diagnostics sends

Remote diagnostics sends the same lines Foundry writes to its log files, and a report when an error occurs. Logs are meant for troubleshooting, so they are not anonymous: they can contain file paths, computer and network names, addresses and the names of your tenant or domain. Passwords and other authentication secrets that Foundry recognizes are masked before a line is written or sent.

Turning the switch off stops new logs from being sent. It does not delete logs already received, which expire after 7 days.

## What is never sent as telemetry

Passwords, passphrases and tokens; Wi-Fi network names; IP addresses; file and folder paths; disk identifiers; computer names; user names, domain names and email addresses; tenant and application identifiers; certificate thumbprints; Windows Autopilot profile names and group tags; serial numbers and hardware hashes.

PostHog necessarily sees the public IP address a connection comes from. Foundry lets PostHog derive an approximate location from it for usage events, and turns this off for error reports.

## Turn data collection off

1. In Foundry OSD, open **Settings**.
2. Turn off **Enable telemetry**, **Enable remote diagnostics**, or both.
3. Restart Foundry OSD. Remote diagnostics stops at once, telemetry at the next start.
4. Create your media again, or update your USB drives. Each media keeps the choice that was in force when it was created.

The choice is stored for the workstation and applies to every Windows account that uses Foundry OSD on it. To block the data at the network level instead, block `eu.i.posthog.com`: Foundry works without it. See [Network endpoints](network-endpoints.md).

## Before you share files

These rules cover what Foundry sends by itself. A log, an exported archive or a screenshot that you attach to a bug report is not filtered again: read it first. [Logs and support information](../troubleshooting/logs-and-support.md) explains the sanitized export.
