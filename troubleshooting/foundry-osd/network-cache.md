# Network cache troubleshooting

Use this page for a message of the **Network cache** page of Foundry OSD, or when devices download from the Internet what the shares should provide. How the cache works is described in [Network cache](../../foundry-osd/network-cache.md).

When Foundry Deploy stops before its wizard opens with a window titled **Network cache not available**, see [Network cache not available](../deployment/before-deployment-starts.md#network-cache-not-available).

**Collect** names the Foundry OSD log, `Foundry.log`, or the Foundry Deploy log, `FoundryDeploy.log`: see [Log locations](../logs-and-support.md#log-locations). Never send an account password.

## Symptom index

| What you see | Where | Go to |
| --- | --- | --- |
| "The shares do not have your latest changes. Select Publish.", or a share named with "did not receive your latest changes" or "no longer has your published content" | Network cache page | [Shares not up to date](#not-up-to-date) |
| A dialog titled **Publishing to the network cache failed** | Network cache page | [Publishing failed](#publish-failed) |
| "This PC cannot sign what it publishes to the shares ..." | Network cache page | [This PC cannot publish](#signing-key) |
| "Media with the network cache on cannot be created because this PC has no trusted key ..." | Network cache page | [No trusted key](#no-trusted-key) |
| "These network cache shares did not receive the imported content: ..." | Result of a media creation | [Media created, shares not updated](#media-shares-not-updated) |
| **Cannot be reached** or **Cannot be read** in the space columns | Network cache page | [Share cannot be measured](#share-cannot-be-measured) |
| A message of the **Clean up share** dialog | Network cache page | [Clean up](#clean-up) |
| Devices download Windows images or driver packs from the Internet although they are on the share, or never add them to the share | Target device | [Devices do not use the cache](#devices-do-not-use-the-cache) |

## "The shares do not have your latest changes. Select Publish." <a href="#not-up-to-date" id="not-up-to-date"></a>

**Where:** Warning bar of the **Network cache** page. The **Network cache** item of the navigation pane shows **Outdated** or **Not published**. Two other forms name a share:

- "`\\fileserver\foundry` did not receive your latest changes. Select Publish."
- "`\\fileserver\foundry` no longer has your published content. Select Publish."

**Cause:** You changed the custom Windows images, the custom driver sets or the post-installation settings since the last publication; or the last publication did not reach the named share; or the content published on the share was deleted or moved.

**Fix:**

1. Check that the named share is reachable from this PC and that its account can write there.
2. Select **Publish**.

Until you do, devices use the shares as described in [Publication state of each share](../../foundry-osd/network-cache.md#publication-state-of-each-share).

**Collect:** Nothing.

## Publishing to the network cache failed <a href="#publish-failed" id="publish-failed"></a>

**Where:** Dialog shown after **Publish**.

**Cause:**

- The dialog names the shares that did not receive your changes: a share cannot be reached, or its account cannot write there.
- "The content to publish could not be prepared.": an imported item cannot be read on this PC.
- The dialog repeats the signing key message: see [This PC cannot publish](#signing-key).

**Fix:**

1. For a named share, check that it is reachable and that its account can write, then select **Publish** again.
2. For content that could not be prepared, open the page of the content, such as [Custom Windows images](../../foundry-osd/customization/custom-windows-images.md), fix what it reports, then select **Publish** again.

A dialog titled **Publishing to the network cache canceled** means that **Cancel** was selected: select **Publish** again later.

**Collect:** `Foundry.log`.

## "This PC cannot sign what it publishes to the shares because its signing key could not be read or saved." <a href="#signing-key" id="signing-key"></a>

**Where:** Warning bar of the **Network cache** page, and the **Publishing to the network cache failed** dialog.

**Cause:** Windows could not open the saved data of this PC, for example after a reset of your Windows password.

**Fix:** Follow [When passwords cannot be restored](../../foundry-osd/configuration-sharing/saved-passwords.md#when-passwords-cannot-be-restored). Media that devices already use keep reading the last publication. Once this PC can publish again, create the media again: media created before keep reading the last publication.

**Collect:** `Foundry.log`.

## "Media with the network cache on cannot be created because this PC has no trusted key for the content published to the shares. Check Configuration sharing on the Settings page." <a href="#no-trusted-key" id="no-trusted-key"></a>

**Where:** Warning bar of the **Network cache** page.

**Cause:** This PC follows a folder but has not accepted a reference PC yet, or configuration sharing is unavailable during this session.

**Fix:** Open **Settings** and check the **Configuration sharing** card:

- If it asks you to accept a new reference PC, see [Accept a new reference](../../foundry-osd/configuration-sharing/follow-a-folder.md#accept-a-new-reference).
- If it reads "Publishing and following are unavailable during this session ...", see [Sharing unavailable](configuration-sharing.md#sharing-unavailable).

**Collect:** `Foundry.log`.

## "These network cache shares did not receive the imported content: ..." <a href="#media-shares-not-updated" id="media-shares-not-updated"></a>

**Where:** Result of the creation of an ISO, a USB drive or PXE files. The media is created.

**Cause:** A share cannot be reached from this PC, or its account cannot write there.

**Fix:** Fix the access to each named share, then select **Publish** on the **Network cache** page. Until you do, devices that use those shares try the next share of their IP scope, and stop at start when no share of the scope has your published content.

**Collect:** `Foundry.log`.

## A share shows Cannot be reached or Cannot be read <a href="#share-cannot-be-measured" id="share-cannot-be-measured"></a>

**Where:** **From the internet** and **Imported content** columns of the **Shares** card.

**Cause:** **Cannot be reached**: this PC cannot open the share, because the server is down, the path is wrong, or the account is refused. **Cannot be read**: the share opens, but the account cannot list its content.

**Fix:** Check the path, the server, and the share and folder permissions of the account, then select **Refresh**. **Clean up** stays unavailable for the share until it can be measured.

**Collect:** `Foundry.log`.

## Clean up <a href="#clean-up" id="clean-up"></a>

**Where:** Dialog shown after **Clean up**.

| Message | Cause | Fix |
| --- | --- | --- |
| "`\\fileserver\foundry` could not be cleaned up. Check that the share is reachable and that its account can delete files, then try again." | The share cannot be reached, or its account cannot delete files | Fix the access, then select **Clean up** again |
| "`\\fileserver\foundry` was cleaned up, but some files were kept because they are in use, changed since they were published, or not written by Foundry." | Some files are used by a device, were changed on the share, or were not written by Foundry | Nothing to do. Content published in the last 24 hours is kept too, in case another PC is still publishing. Select **Clean up** again later to delete files that were in use |

**Collect:** `Foundry.log`.

## Devices do not use the cache <a href="#devices-do-not-use-the-cache" id="devices-do-not-use-the-cache"></a>

**Where:** Target device. The deployment works, but the device uses another share than the one you expect, or Windows images and driver packs come from the Internet, or are never added to the share.

**Cause and fix:** Open the Foundry Deploy log of the device and find what it says about the network cache. It names the share it tried and the share it kept.

| Symptom | What the log says | Cause | Fix |
| --- | --- | --- | --- |
| The device uses a share lower in the list of its IP scope | The connection to the first share failed | The path, the user name or the password of the account is wrong, or the account cannot open the share | Check the account on the **Network cache** page and its permissions on the server, then create the media again |
| The device uses a share lower in the list | The first share timed out or did not answer | The file server is down, or the device cannot reach it on port 445 | Bring the server back, or open port 445 between the devices and the server |
| The device uses a share lower in the list | The published content of the first share is missing or out of date | The share did not receive your last publication, or its content was deleted | Select **Publish** in Foundry OSD |
| The device reads from the share but never adds what it downloads | The network cache is connected, but the device cannot write there | The account can read the share but cannot write or delete in it | Give the account the right to write and delete, in the share permissions and in the folder permissions |
| A file comes from the Internet although it is on the share | The file of the share does not match, or its copy stalled or failed, and the device downloads it instead | The copy on the share is damaged or incomplete, or the share stopped answering during the copy | Nothing to do: the device downloads the file from the Internet. If it happens again, select **Clean up** for the share |
| A downloaded file is not on the share after the deployment | The copies to the share did not finish in time | **Finalize deployment** waits up to 2 minutes for the copies, then drops the ones still running | Nothing to do: the next device downloads the file again and adds it. If it happens often, see [Several devices at the same time](../../foundry-osd/network-cache.md#several-devices-at-the-same-time) |

**Collect:** `FoundryDeploy.log` and the IP address of the device.

## Related

- [Network cache](../../foundry-osd/network-cache.md)
- [Before the deployment starts](../deployment/before-deployment-starts.md)
- [Configuration sharing troubleshooting](configuration-sharing.md)
- [Logs and support information](../logs-and-support.md)
