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
| Foundry OSD | When a media creation ends, whether it succeeded, failed or was cancelled | Result, duration, failure details, and the options chosen for the media |
| Windows PE startup | Only when the startup fails | Failure category, last stage reached, elapsed time, and which application failed to start |
| Foundry Connect | When the network is ready | Connection type, what was available, Wi-Fi security type |
| Foundry Deploy | When a deployment ends, whether it succeeded, failed or was cancelled | Result, duration, failure details; device manufacturer and model; the Windows, driver pack and options used |

Every event also carries a format version, the application name and version, its build type, the environment it runs in, its language and architecture, a session identifier, and an anonymous installation identifier created at random by Foundry OSD. The two lists below give every field of each event.

<details>

<summary>Fields reported when a media creation ends</summary>

Values are switches, counts or fixed choices. No name, path or text you typed is included.

| Area | What is reported |
| --- | --- |
| Result | ISO or USB, creation or update; success or failure; duration; an identifier of the operation |
| Failure | Name of the step that failed; kind, reason and code of the failure, each from a fixed list; name of the tool that failed and its exit code. Empty when the creation succeeds |
| Media | Architecture; Windows PE language; standard or Wi-Fi boot image; Secure Boot signature; USB partition style and format mode; whether the Foundry Connect and Foundry Deploy applications put on the media are release builds |
| Drivers | Whether Dell, HP or a custom driver folder is used |
| General | Whether Password protection is on; restart mode and delay; whether a Windows PE time zone is set |
| Network | Whether any network option is on. Ethernet 802.1X: whether it is on, whether a profile is set, whether a certificate is required and whether one is set. Wi-Fi: whether it is on, whether a profile, a network name, a passphrase, an enterprise profile and an enterprise certificate are set, whether a certificate is required, and the security type. Whether profiles and private keys are kept for the installed Windows, overall and for each of Ethernet and Wi-Fi |
| Windows Autopilot | Whether it is on, and the method |
| Domain Join | Whether it is on, the method, the number of domains (up to 32) and of OUs, whether a shared join account is used, whether a default OU is set |
| Customization | Whether any customization page is on |
| OS selection | Whether it is on and whether anything is set; number of allowed languages, releases, editions and licensing types; whether a default is set for each of the four; the default update offset (0 to 11) |
| Custom Windows images | Whether they are on, how many are included, and whether the default is a catalog or a custom image |
| Unattend | Whether it is on, the number of answer files (up to 100), and whether the default is Foundry's settings or a custom file |
| Machine naming | Whether it is on, the mode, the number and types of name components, separator, casing, truncation directions, and whether the technician may edit the name |
| OOBE | Whether it is on, and each choice: license terms, diagnostic data level, privacy screen, tailored experiences, advertising ID, speech recognition, inking and typing, location |
| Post-installation | Whether it is on, the number of actions and of enabled actions (up to 1,000), and the count of each type: PowerShell script, command, software installation, restart |
| Optional features | Whether it is on, the number of features set, to enable and to disable, the number of feature categories, and whether Windows source files are needed |
| AppX removals | Whether it is on, the number of apps, and whether the selection matches one preset, several or none |
| AI components | Whether it is on, each of the eight options, and how many are chosen |

</details>

<details>

<summary>Fields reported by the other events</summary>

| Event | What is reported |
| --- | --- |
| Foundry OSD, once a day | Proxy method; authentication mode of a manual proxy |
| Windows PE startup failure | Failure category; last stage reached; elapsed time; which application failed, its exit code and how far its start went; version, release or development build, and architecture of that application |
| Foundry Connect, network ready | Connection type (Ethernet or Wi-Fi); window layout; whether Ethernet and Wi-Fi were available; Wi-Fi security type and the origin of the Wi-Fi connection; whether Ethernet 802.1X and Wi-Fi were set on the media |
| Foundry Deploy, result | Success, cancellation, duration, number of steps completed, an identifier of the operation; whether it was a test run and the session mode |
| Foundry Deploy, failure | Name of the step and of the operation that failed; kind, code and reason of the failure |
| Foundry Deploy, device | Manufacturer, model, and whether it is a virtual machine |
| Foundry Deploy, Windows | Catalog or custom image; product, release, build, update month, architecture, language, edition, licensing, image index; Foundry's settings or a custom answer file |
| Foundry Deploy, options | Driver pack source, manufacturer and model; whether firmware updates were on; Windows Autopilot: on or off, method, upload state, whether a group tag was chosen; Domain Join: on or off, method, how the domain and the OU were chosen, state; restart mode and delay |

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
