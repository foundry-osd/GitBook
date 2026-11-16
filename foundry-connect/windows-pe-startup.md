# Windows PE startup

When a target device starts from deployment media, a console titled **Foundry WinPE** prepares Windows PE, opens Foundry Connect, then opens Foundry Deploy. You only watch it: this page says what each line means and which waits are normal.

<figure>
  <img src="../.gitbook/assets/shared-bootstrap-01-download-progress.png" alt="Foundry WinPE console with five startup stages and a download progress bar">
  <figcaption>The five stages, the current action with its progress bar, and the elapsed time.</figcaption>
</figure>

## What you see, in order

Each of the five stages goes from **Waiting** to **In progress**, then **Done**. The line under the list says what is happening now.

| Stage | Line under the list | What happens |
| --- | --- | --- |
| **Environment** | "Preparing network access" | Windows PE starts its wired and Wi-Fi services. |
| **Network connection** | "Waiting for Foundry Connect" | Foundry Connect opens from the program on the media. The stage stays **In progress** until you continue from [Foundry Connect](README.md). |
| **Clock and time zone** | "Preparing the system clock and time zone" | The clock is set from the Internet and the time zone is applied. |
| **Deployment files** | "Preparing Foundry PostInstall" | The post-installation application is taken from the media, or downloaded when the media does not carry it. |
| **Deployment application** | "Starting Foundry Deploy" | Foundry Deploy opens. |

During a download of the post-installation application, the status reads **Downloading**, **Verifying**, then **Extracting**, with a progress bar. Startup is complete when the last stage shows **Ready** and the line under the list reads "Continue in Foundry Deploy."

## Waits that are normal

- **Network connection**: as long as you need in [Foundry Connect](network-readiness.md).
- **Clock and time zone**: up to 50 seconds on a network that filters the lookups (five lookups of 10 seconds).
- **Deployment files**: a download of the post-installation application can run for 15 minutes and is tried 3 times.
- **Deployment application**: up to 2 minutes before the Foundry Deploy window appears.

On a device with no cable, the Internet is out of reach before Foundry Connect. A yellow line can then appear and stay until the end: "Warning: Internet clock could not be resolved. Boot will continue without clock correction."

It is expected on a Wi-Fi-only device. **Environment** ends with **Done (warning)** and the final screen adds a `Session:` and a `Log:` line. No action is needed when the last stage ends with **Ready**.

## Which version runs

The media runs Foundry Connect and Foundry Deploy, both inside the Foundry WinPE program, and the post-installation application of the Foundry OSD version that created it, never a newer one. To use newer versions, update Foundry OSD, then recreate the ISO or the PXE files, or [update the USB drive](../foundry-osd/media/update-usb.md).

If the post-installation application on the media is missing or damaged, the console downloads the same version again from the Foundry release that created the media and shows a yellow line such as "Warning: Foundry.PostInstall was repaired from release v26.11.2.1." Without a valid copy, startup stops: see [This boot stage could not be completed](../troubleshooting/windows-pe-startup.md#boot-stage-not-completed).

When a newer Foundry release is published, Foundry Deploy checks online once the network is ready and shows a notice next to its version. Foundry Connect does not check online: it shows a notice only from what the media itself records, for example when the Foundry OSD version that created the media cannot be identified. Neither notice blocks anything: see [New version available](../troubleshooting/network.md#new-version-available).

## What needs Internet access

- **Foundry Connect** and **Foundry Deploy** are part of the Foundry WinPE program on the media and start without a network.
- **The post-installation application** is on the media only with Domain Join. Otherwise it is downloaded from GitHub, from the release that created the media. An ISO, PXE files and a USB drive created without cache download it at every start. A USB drive created with cache keeps it on its **Foundry Cache** partition and reuses it at the next starts; it downloads it again only after the drive was updated to another version. When it cannot be obtained, startup stops at **Deployment files**.

See [Network endpoints](../reference/network-endpoints.md) for the hosts. What the media carries, and where, is in [What each media type carries](../foundry-osd/media/README.md#what-each-media-type-carries).

## When the clock and the time zone are set

The clock is corrected from the Internet before Foundry Connect when the network already works, otherwise at **Clock and time zone**. The time zone is applied at that stage, from the [Windows PE time zone](../foundry-osd/general.md#windows-pe-time-zone) setting of the media: a fixed zone, or the zone of the public IP address of the network, with UTC when that lookup gets no answer.

## If startup stops

A stage that shows **Failed** or **Cancelled** ends the startup, and the line under the list gives the reason. Find that text in [Windows PE startup troubleshooting](../troubleshooting/windows-pe-startup.md).
