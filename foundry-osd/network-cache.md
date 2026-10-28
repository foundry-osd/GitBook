# Network cache

The Network cache page lets Foundry Deploy use SMB shares of your network as a shared cache. Each device picks its shares from its IP address. It reads Windows images, driver packs and your imported content from the share before the Internet, and publishes what it downloads back to the share for the next devices.

You change imported content in Foundry OSD, then select **Publish**: devices pick up the change on their next deployment, with the boot images they already have.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-network-cache-01-overview.png`
- **Capture:** Show the Network cache page turned on and up to date, with one account, two shares **Up to date** with their space measured, the **Publish** and **Clean up** buttons, and two IP scopes, one subnet and one range.
{% endhint %}

## How a device uses the cache

When Foundry Deploy starts, it compares the IP addresses of the device with the **IP scopes** of the page. The most specific scope that contains one of them chooses the shares, and the device tries them in the order of that scope. It keeps the first share that answers for the whole deployment.

Before its wizard opens, Foundry Deploy checks that the share answers and that your published content is complete. If it is not, Foundry Deploy stops with one message that says what to fix: see [If a device stops at start](#if-a-device-stops-at-start). Nothing is erased at that point.

For each file it needs, Foundry Deploy then looks in this order:

1. The cache of the USB drive it started from, when there is one.
2. The share.
3. The Internet. The device then publishes the file to the share, when its account can write there.

| Shared on the cache | Where on the share |
| --- | --- |
| Windows images of the catalog, driver packs, Microsoft Update Catalog drivers and firmware | `Foundry\Downloads\`, published by devices |
| [Custom Windows images](customization/custom-windows-images.md), [custom driver sets](customization/custom-drivers.md) and [post-installation](customization/post-installation.md) content you imported | `Foundry\Cache\`, published by Foundry OSD |

Foundry Deploy checks the hash of every file it reads from a share. A file that is incomplete or does not match is ignored, and the device downloads it from the Internet. Foundry Deploy runs content you imported only when Foundry OSD published it: see [Security and credentials](../reference/security-and-credentials.md#network-cache-publication). Foundry Connect and Foundry Deploy themselves are not taken from the share.

A device that matches no scope, or reaches none of its shares, does not deploy. This protects the disk of a device that would otherwise miss its custom image or its post-installation content. To let such devices go on, add a [backup scope](#backup-scope-for-every-other-device).

## Before you start

- An SMB share for each site, written `\\server\share`. Foundry creates its `Foundry` folder at the root of the share.
- An account that can read, write and delete in that share, both in the share permissions and in the folder permissions. Use an account dedicated to this cache: anyone who can start the media and knows the Deployment password can use it.
- **Password protection** turned on in [General](general.md#password-protection) when a share has an account.
- The devices reach the file server on port 445 from Windows PE: see [Network endpoints](../reference/network-endpoints.md#target-device-in-windows-pe).
- The PC that runs Foundry OSD reaches the shares too, to publish imported content and to measure them or clean them up.

A share without an account is read without credentials and devices never write to it. Use one only when your file server allows reading without credentials, and fill it from Foundry OSD or from devices of another scope.

## Turn on the network cache

1. In the navigation pane, under **General**, open **Network cache**.
2. Turn the switch at the top right to **Enabled**.
3. Add the accounts, then the shares, then the IP scopes, as described below. Each change is saved at once.
4. If the page shows "Shares with an account require Deploy password protection, because a PXE boot image can be downloaded by anyone on the network. Turn it on in General.", turn on **Password protection** in [General](general.md#password-protection).
5. Select **Publish**, as described in [Publish](#publish).

Turn the switch to **Disabled** to stop using the cache. The accounts, shares and scopes are kept for later.

### Accounts

Under **Accounts**, select **Add**. Type the **User name**, such as `DOMAIN\user` or `user@domain`, then the **Password** twice, and select **Add**.

When the file server is not in a domain, for example a PC or a NAS in a workgroup, create a local account on that server and give it the rights on the share and on its folder. Type it with the name of the server, such as `FILESERVER\foundry-cache`, even when the share path uses an IP address. Do not use `.\foundry-cache`: the dot names the device that connects, not the server.

The table shows each account with its password state, **Saved** or **Missing**, and the shares that use it. To change a password, select the account, then **Edit**: leave the password empty to keep the current one. An account used by a share cannot be removed; remove it from the share first.

The account passwords are passwords of your configuration, saved like the others: see [Saved passwords and files](configuration-sharing/saved-passwords.md). On the media they are protected by the Deployment password; the share paths and user names are not.

### Shares

Under **Shares**, select **Add**. Type the **Path**, such as `\\fileserver\foundry`, choose an **Account** or **No account**, and select **Add**.

A path must start with `\\server\share`, and a share can be listed once. Removing a share also removes it from the IP scopes that use it.

### IP scopes

Under **IP scopes**, select **Add**. **Add** stays disabled until there is a share.

1. Type a **Name**, such as the name of the site.
2. Choose a **Type** and type its **Value**:

   | Type | Value | Example |
   | --- | --- | --- |
   | **Subnet** | A network address and its prefix length, IPv4 or IPv6 | `10.10.0.0/16`, `fd00:10::/48` |
   | **Range** | A first and a last address of the same family, separated by `-` | `10.20.0.10-10.20.0.200` |
   | **Address** | One address | `10.20.0.15` |

3. Under **Shares**, check the shares of this scope and order them with the arrows. Devices try the checked shares from top to bottom.
4. Select **Add**.

When a device matches several scopes, the most specific one wins: the scope with the fewest addresses. Between two scopes of the same size, the one higher in the table wins.

#### Backup scope for every other device

A device that is in no scope stops at start. To accept every device, add a scope of type **Subnet** with the value `0.0.0.0/0` for IPv4, and a second one with `::/0` for IPv6, and check a backup share in each. The most specific scope still wins, so these scopes only serve the devices that no other scope contains.

## Publish

**Publish** sends your imported content and the settings that select it to every share of the page. Select it each time you change the custom Windows images, the custom driver sets or the post-installation settings, including their defaults, and each time you add a share.

1. On the **Shares** card, select **Publish**. The button is available when the network cache is on and the page shows no error.
2. Follow the **Publishing to the network cache** dialog. It shows one share after the other and has a **Cancel** button.
3. The dialog ends with **Published to the network cache** and "The shares have your latest changes."

Devices use the new content on their next deployment. You do not create the boot images again, and you do not redistribute them: an ISO, a USB drive or a PXE boot image you already handed out reads the published content from its share. Creating an ISO, a USB drive or [PXE files](media/pxe-deployment.md) publishes to the shares too.

Only the content is published this way. The accounts, shares and IP scopes travel with the boot image: after you change them, create the media again.

### Up to date or not published

The page shows one of two states.

| State | What you see |
| --- | --- |
| **Up to date** | The **Status** column of each share reads **Up to date**. |
| **Not published** | The **Status** column of the shares without your latest changes reads **Not published**. A warning bar reads "The shares do not have your latest changes. Select Publish." The **Network cache** item of the navigation pane shows a **Caution** badge, and the **Network cache** row of [Start](media/README.md) reads **Needs attention** with the reason **Not published**. |

A share whose settings are incomplete shows what is missing in its **Status** column instead. When you open the page, Foundry OSD also checks that each share still holds what you published. A share whose published content was deleted or moved reads **Not published**, and the bar names it: "`\\fileserver\foundry` no longer has your published content. Select Publish." When a share did not receive the last publication, the bar names it, as in "`\\fileserver\foundry` did not receive your latest changes. Select Publish." Check that the share is reachable and that its account can write, then select **Publish** again.

**Not published** does not block media creation, because creating media publishes too.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-network-cache-02-not-published.png`
- **Capture:** Show the Network cache page with the **Not published** warning bar, the **Caution** badge on the **Network cache** navigation item, and the **Publish** button on the **Shares** command bar.
{% endhint %}

### Several PCs running Foundry OSD

Several PCs running Foundry OSD? Use [configuration sharing](configuration-sharing.md) so that every device gets the same configuration. Without it, each PC publishes its own configuration, and each boot image gets the configuration of the PC that created it.

On a PC that [follows a folder](configuration-sharing/follow-a-folder.md), **Publish** is not shown, the shares read **Ready**, and the page is never **Not published**: the reference PC publishes. Media created on the follower still copy missing files to the shares.

## Imported content

While the network cache is on with at least one share, the custom Windows images, custom driver sets and post-installation content of your configuration stay on the shares only. ISO files and USB drives no longer carry them, and **Start** reads "The network cache is on, so ISO and USB media do not include the custom Windows images, custom driver sets and post-installation content. Devices take them from their network cache share." Without the network cache, every media carries its content and works on its own.

Foundry OSD publishes this content to every share of the page when you select **Publish** and each time it creates an ISO, a USB drive or [PXE files](media/pxe-deployment.md). The progress dialog of the media then has a last but one step, **Publishing to the network cache**, with the progress of the copy to each share. Foundry OSD opens each share with its account, or with your Windows account when the share has none.

A share that cannot be reached or written does not make the media fail. The result then adds "The imported content could not be published to these network cache shares:", followed by the shares. Check them, then select **Publish**: devices that use those shares do not deploy until then.

On the device, Foundry Deploy offers the custom images and driver sets of its share, and takes post-installation content from it. A device in no scope, or that reaches none of its shares, stops at start with the message described in [If a device stops at start](#if-a-device-stops-at-start); to deploy such devices, add a [backup scope](#backup-scope-for-every-other-device). To deploy imported content with no share at all, turn the network cache switch to **Disabled** and create the media, which then carries its content.

## Space and clean up

Two columns show the space each share uses. Point to a column header to read what it counts.

| Column | What it counts |
| --- | --- |
| **From the internet** | Windows and the driver packs that devices downloaded from the internet and published to the share. |
| **Imported content** | The custom images, custom driver sets and post-installation scripts that Foundry OSD published. |

Foundry OSD measures the shares when you open the page; select **Refresh** to measure them again. A share that cannot be opened shows **Cannot be reached**.

To free space on a share:

1. Select the share.
2. Select **Clean up**, then **Clean up** in the confirmation dialog: "Delete the downloads on `\\fileserver\foundry` and the imported content your published configuration no longer uses? Devices download again what they need."

Foundry OSD deletes every download of the share and the imported content that no publication uses any more. Content that is still used is kept, including the content that boot images created by another PC, or before a change of reference PC, still read. Devices download again from the Internet what was deleted, and publish it again.

Files in use, changed since they were published, or not written by Foundry are kept, and a dialog says so: "`\\fileserver\foundry` was cleaned up, but some files were kept because they are in use, changed since they were published, or not written by Foundry."

## Several devices at the same time

Devices never wait for each other. When two devices need the same file and the share does not have it yet, both download it from the Internet; the first one to finish publishes it, and the other keeps its own copy. A device reads a file only once it is complete on the share.

A device writes a file under a temporary name and gives it its final name only once it is complete. A temporary file left by a device that stopped is deleted after 24 hours, by the next device that publishes the same file. **Clean up** deletes them too.

## Check the result

1. Open [Start](media/README.md). In the **General** group, the **Network cache** row shows the number of shares and IP scopes. It must not show **Needs attention**.
2. Create the media and deploy a test device whose address is in one of the scopes.
3. In the Foundry Deploy log, look for "Network cache connected", followed by the share and whether the device can write there: see [Logs and support information](../troubleshooting/logs-and-support.md).
4. Deploy a second device in the same scope. Its Windows image and driver pack are read from the share instead of the Internet.
5. Change the default Windows image, select **Publish**, and deploy again with the same boot image: the new default is offered.

## If a device stops at start

Foundry Deploy shows one message, **Network cache not available**, with the cause and the place to fix it, either in Foundry OSD or on the server. For some causes it offers **Retry**; **Close** ends Foundry Deploy without deploying. The four messages, their causes and their fixes are listed in [Network cache not available](../troubleshooting/deployment/before-deployment-starts.md#network-cache-not-available).

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-deploy-network-cache-01-startup-error.png`
- **Capture:** Show the Foundry Deploy **Network cache not available** message for a share that is not responding, with the **Retry** and **Close** buttons.
{% endhint %}

## If devices do not use the cache

The Foundry Deploy log says why a device did not use a share:

| Log line | Check |
| --- | --- |
| "Network cache not used. Outcome=NoMatchingScope" | None of the IP scopes contains an address of the device. Add its subnet or range, or a [backup scope](#backup-scope-for-every-other-device). |
| "Network cache share connection failed", with a `Win32Error` | The path, the user name and the password of the account, and that the account can open the share. |
| "Network cache share connection timed out" or "Network cache share did not answer" | That the file server is up and that the device reaches it on port 445. |
| "Network cache connected", with `CanWrite=False` | The share is read, but the device does not publish: give the account the right to write and delete in the share and in its folder. |
| "Network cache content check failed" | The published content is missing or incomplete: select **Publish** in Foundry OSD. |

## Related

- [General](general.md)
- [Deploy with PXE](media/pxe-deployment.md)
- [Configuration sharing](configuration-sharing.md)
- [Security and credentials](../reference/security-and-credentials.md)
- [Before the deployment starts](../troubleshooting/deployment/before-deployment-starts.md)
