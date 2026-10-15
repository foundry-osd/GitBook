# Windows PE startup

When a target device starts from deployment media, a console titled **Foundry Bootstrap** prepares Windows PE, opens Foundry Connect, then gets Foundry Deploy and opens it. You only watch it: this page says what each line means and which waits are normal.

<figure>
  <img src="../.gitbook/assets/shared-bootstrap-01-download-progress.png" alt="Foundry Bootstrap console with five startup stages and a download progress bar">
  <figcaption>The five stages, the current action with its progress bar, and the elapsed time.</figcaption>
</figure>

## What you see, in order

Each of the five stages goes from **Waiting** to **In progress**, then **Done**. The line under the list says what is happening now.

| Stage | Line under the list | What happens |
| --- | --- | --- |
| **Environment** | "Preparing network access" | Windows PE starts its wired and Wi-Fi services. |
| **Network connection** | "Preparing Foundry Connect", then "Waiting for Foundry Connect" | Foundry Connect opens. When GitHub is reachable, its latest release is used: downloaded, or taken from a USB drive that already holds it. The stage stays **In progress** until you continue from [Foundry Connect](README.md). |
| **Clock and time zone** | "Preparing the system clock and time zone" | The clock is set from the Internet and the time zone is applied. |
| **Deployment files** | "Preparing the deployment application", then "Preparing Foundry PostInstall" | Foundry Deploy and the post-installation application are downloaded, or taken from the USB drive. On a USB drive, the latest Foundry Connect is also fetched here and kept for the next start. |
| **Deployment application** | "Starting Foundry Deploy" | Foundry Deploy opens. |

During a download, the status reads **Downloading**, **Verifying**, then **Extracting**, with a progress bar. Startup is complete when the last stage shows **Ready** and the line under the list reads "Continue in Foundry Deploy."

## Waits that are normal

- **Network connection**: up to 5 seconds on "Preparing Foundry Connect" without a network, longer when Foundry Connect is downloaded, then as long as you need in [Foundry Connect](network-readiness.md).
- **Clock and time zone**: up to 50 seconds on a network that filters the lookups (five lookups of 10 seconds).
- **Deployment files**: a download can run for 15 minutes and is tried 3 times.
- **Deployment application**: up to 2 minutes before the Foundry Deploy window appears.

On a device with no cable, the Internet is out of reach before Foundry Connect. Two yellow lines can then appear and stay until the end:

- "Warning: Internet clock could not be resolved. Boot will continue without clock correction."
- "Warning: Online runtime verification failed. Continuing with the original application provisioned on the boot media."

Both are expected on a Wi-Fi-only device. The first two stages end with **Done (warning)** and the final screen adds a `Session:` and a `Log:` line. No action is needed when the last stage ends with **Ready**.

## What needs Internet access

- **Foundry Connect** is on the media and starts without a network. When GitHub is reachable before it opens, its latest release is used instead.
- **Foundry Deploy** is never on the media. It is downloaded from GitHub with the post-installation application, which the media carries only with Domain Join. An ISO downloads them at every start. A USB drive keeps the downloads on its **Foundry Cache** partition and repeats them only after a new release.
- **Every start needs an answer from `api.github.com`**, which names the release to use, even when the applications are already on the USB drive. Without it, startup stops at **Deployment files**.

See [Network endpoints](../reference/network-endpoints.md) for the hosts. What the media carries, and where, is in [What each media type carries](../foundry-osd/media/README.md#what-each-media-type-carries).

## When the clock and the time zone are set

The clock is corrected from the Internet before Foundry Connect when the network already works, otherwise at **Clock and time zone**. The time zone is applied at that stage, from the [Windows PE time zone](../foundry-osd/general.md#windows-pe-time-zone) setting of the media: a fixed zone, or the zone of the public IP address of the network, with UTC when that lookup gets no answer.

## If startup stops

A stage that shows **Failed** or **Cancelled** ends the startup, and the line under the list gives the reason. Find that text in [Windows PE startup troubleshooting](../troubleshooting/windows-pe-startup.md).
