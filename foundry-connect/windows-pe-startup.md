# Windows PE startup

When a target device starts from deployment media, a console titled **Foundry WinPE** prepares Windows PE, then starts Foundry. Foundry is one application: it shows Foundry Connect, then continues into Foundry Deploy without closing or restarting. Everything it runs is already on the media: nothing is downloaded, repaired or unpacked at startup. You only watch it: this page says what each line means and which waits are normal.

<figure>
  <img src="../.gitbook/assets/shared-bootstrap-01-download-progress.png" alt="Foundry WinPE console with its startup stages">
  <figcaption>The startup stages, the current action, and the elapsed time.</figcaption>
</figure>

## What you see, in order

The console lists two stages. Each goes from **Waiting** to **In progress**, then **Done**. The line under the list says what is happening now.

| Stage | Line under the list | What happens |
| --- | --- | --- |
| **Environment** | "Preparing network access" | Windows PE starts its wired and Wi-Fi services. |
| **Foundry** | "Starting Foundry" | One Foundry window opens from the program on the media and shows first [Foundry Connect](README.md), then Foundry Deploy. The stage stays **In progress** as long as Foundry runs. |

The console is only a progress screen. The work happens in the Foundry window: once Foundry Connect has confirmed the network and you continue, the clock and the time zone are set, then Foundry Deploy replaces the Foundry Connect content in the same window, with the theme, the language and the keyboard layout you chose in Foundry Connect. The console shows nothing more for those steps.

If Foundry stops unexpectedly, the console shows a yellow warning "Foundry stopped unexpectedly with exit code" and a number, and a full-screen page offers the next step: see [If Foundry stops unexpectedly](#if-foundry-stops-unexpectedly).

## Waits that are normal

- **Foundry window**: up to 2 minutes before the Foundry window appears with Foundry Connect.
- **Foundry Connect**: as long as you need in [Foundry Connect](network-readiness.md).
- **Clock and time zone**: up to 50 seconds between Foundry Connect and Foundry Deploy on a network that filters the lookups (five lookups of 10 seconds).

On a device with no cable, the Internet is out of reach before Foundry Connect. A yellow line can then appear and stay until the end: "Warning: Internet clock could not be resolved. Boot will continue without clock correction."

It is expected on a Wi-Fi-only device. **Environment** ends with **Done (warning)**. No action is needed when Foundry Connect then opens.

## Which version runs

The media runs Foundry Connect and Foundry Deploy, both inside one Foundry WinPE application, and carries the post-installation application, all of the Foundry OSD version that created it, never a newer one. To use newer versions, update Foundry OSD, then recreate the ISO or the PXE files, or [update the USB drive](../foundry-osd/media/update-usb.md).

The post-installation application is stored in the boot image like the Foundry WinPE program. If it is missing or damaged, nothing repairs it at startup: Foundry Deploy stops before it touches the disk and asks you to recreate the media (see [Checks and image download](../troubleshooting/deployment/checks-and-image-download.md#post-installation-preparation-fails)).

Media created with Foundry OSD v26.10.18.1 or earlier download Foundry Connect and Foundry Deploy from the latest release at each start. They now show "This boot media is obsolete. Update Foundry OSD and recreate the media." instead of Foundry Connect or Foundry Deploy: see [This boot media is obsolete](../troubleshooting/windows-pe-startup.md#boot-media-obsolete). If such media cannot reach GitHub (no network, or GitHub limits requests from your address), they start the application stored on the media and deploy as before, until you recreate them.

When a newer Foundry release is published, Foundry Deploy checks online once the network is ready and shows a notice next to its version. Foundry Connect does not check online: it shows a notice only from what the media itself records, for example when the Foundry OSD version that created the media cannot be identified. Neither notice blocks anything: see [New version available](../troubleshooting/network.md#new-version-available).

## What needs Internet access

- **Foundry Connect**, **Foundry Deploy** and **the post-installation application** are on the media and start without a network. The startup itself needs no Internet access.
- **The Internet** is used later: by Foundry Deploy for the Windows image and the driver packs, and, with the interactive Windows Autopilot upload, to download Foundry Autopilot Interactive of its own release.

See [Network endpoints](../reference/network-endpoints.md) for the hosts. What the media carries, and where, is in [What each media type carries](../foundry-osd/media/README.md#what-each-media-type-carries).

## When the clock and the time zone are set

The clock is corrected from the Internet before Foundry Connect when the network already works, and again between Foundry Connect and Foundry Deploy. The time zone is applied between them, from the [Windows PE time zone](../foundry-osd/general.md#windows-pe-time-zone) setting of the media: a fixed zone, or the zone of the public IP address of the network, with UTC when that lookup gets no answer.

## If startup stops

A stage that shows **Failed** or **Cancelled** ends the startup, and the line under the list gives the reason. Find that text in [Windows PE startup troubleshooting](../troubleshooting/windows-pe-startup.md).

## If Foundry stops unexpectedly <a href="#if-foundry-stops-unexpectedly" id="if-foundry-stops-unexpectedly"></a>

When Foundry closes without finishing, after its window appeared, a full-screen page titled **Foundry stopped** says so, in the language you chose in Foundry Connect, or else the language of Windows PE. It shows the folder that holds the logs. **Relaunch** appears only when the deployment had not yet started to prepare the target disk: Foundry then starts again from Foundry Connect. Otherwise the disk may already have been changed, only **Close** is offered, and you restart the device to begin a new deployment. See [Foundry stopped](../troubleshooting/windows-pe-startup.md#foundry-stopped).
