# Foundry OSD

Foundry OSD is the desktop application in which the administrator sets the options of a deployment and creates the media. This page is a tour of its window; the pages of this section then follow the navigation pane from top to bottom.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-home-01-workflow.png`
- **Capture:** Show the whole Foundry OSD window on **Home** with the ADK ready: full navigation pane including the **Domain Join** section and the footer items, the four action tiles, **Media readiness status** and the **ADK** and **Configuration** cards.
{% endhint %}

## Navigation pane

The pane on the left lists every page. Sections and items appear in this order:

| Section | Items |
| --- | --- |
| **General** | **Home**, **ADK**, **General**, **Start** |
| **Network** | **Ethernet 802.1X**, **Wi-Fi** |
| **Windows Autopilot** | **JSON profile**, **Zero-Touch**, **Interactive** |
| **Domain Join** | **Zero-Touch**, **Interactive** |
| **Customization** | **OS selection**, **Custom Windows images**, **Unattend**, **Machine naming**, **OOBE**, **Post-installation**, **Optional features**, **AppX removals**, **AI components** |

The bottom of the pane holds five more items:

| Item | What it does |
| --- | --- |
| **Update** | Appears only when a newer Foundry OSD is available. It becomes **Apply update** once the download is finished. See [Settings](settings.md#update-app). |
| **Documentation** | Opens this site in your browser. |
| **Report a bug** | Opens the bug report form of the Foundry project on GitHub. |
| **About** | Shows the version, the release notes, the contributors and the licenses. |
| **Settings** | Opens the [Settings](settings.md) of the application itself. |

Each configuration page also has a **Documentation** button on the right of its header that opens the page of this site about that screen.

## Home

**Home** summarizes the state of the workstation. Its four tiles are shortcuts: **Open ADK**, **Configure media** (opens **General**), **Review and start** (opens **Start**) and **Open documentation**.

Under **Media readiness status**, the **ADK** step shows **Ready** or the status of the ADK page, such as **ADK is not installed**. The **General** and **Start** steps show **Ready**, **Needs attention** or, while the ADK is not ready, **Requires ADK**. The **ADK** card gives the installed version and the state of the Windows PE add-on. The **Configuration** card gives the architecture, the Windows PE language, the Secure Boot signature and the drivers chosen in **General**.

## Status badges

A small colored badge can appear to the right of an item. Point at it to read its text.

| Badge text | Shown on | Meaning |
| --- | --- | --- |
| **ADK ready** / **ADK not ready** | **ADK** | Whether Foundry OSD can create media. |
| **Configured** | Network and Customization pages | The settings of the page are valid, or left at their defaults, and the media will use them. |
| **Active provisioning mode** | Windows Autopilot and Domain Join pages | This method is the one the media will use. |
| **Needs attention** | Any page, including **General** | The feature is turned on but something is missing. Media creation is blocked until you fix it. |

An item without a badge is not configured, which is normal for a feature you do not use.

## When items are grayed out

- The ADK is not ready: only **Home**, **ADK**, **Settings** and the other items at the bottom of the pane can be opened, and the **Configure media** and **Review and start** tiles on **Home** are disabled. Finish the [ADK](adk.md) page first.
- An operation is running: while Foundry OSD installs the ADK or creates media, a dialog titled **Operation in progress** covers the window and the whole pane is disabled until the operation ends.

## Find the right page

| I want to ... | Go to |
| --- | --- |
| Install or repair the Windows ADK components | [ADK](adk.md) |
| Choose the architecture, language, restart behaviour, drivers or a Deployment password | [General](general.md) |
| Give Windows PE access to Wi-Fi or an 802.1X network | [Network](network/README.md) |
| Register devices with Windows Autopilot | [Windows Autopilot](autopilot/README.md) |
| Join devices to an Active Directory domain | [Domain Join](domain-join/README.md) |
| Choose the Windows offered, name computers, shape OOBE, run my own actions | [Customization](customization/README.md) |
| Create an ISO file or a USB drive | [Start: create deployment media](media/README.md) |
| Change the language, proxy, telemetry or update behaviour of the app | [Settings](settings.md) |
| Save, move or share my deployment settings | [Settings backup and sync](deployment-profiles.md) |
