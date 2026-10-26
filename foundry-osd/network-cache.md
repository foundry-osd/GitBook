# Network cache

The Network cache page lets Foundry Deploy use SMB shares of your network as a shared cache. Each device picks its shares from its IP address. It reads Windows images, driver packs and your imported content from the share before the Internet, and publishes what it downloads back to the share for the next devices. Without a share it can use, a deployment works as before.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-network-cache-01-overview.png`
- **Capture:** Show the Network cache page turned on, with one account, two shares with their **Space used** measured, and two IP scopes, one subnet and one range.
{% endhint %}

## How a device uses the cache

When Foundry Deploy starts, it compares the IP addresses of the device with the **IP scopes** of the page. The most specific scope that contains one of them chooses the shares, and the device tries them in the order of that scope. It keeps the first share that answers for the whole deployment.

For each file it needs, Foundry Deploy then looks in this order:

1. The cache of the USB drive it started from, when there is one.
2. The share.
3. The Internet. The device then publishes the file to the share, when its account can write there.

| Shared on the cache | Where on the share |
| --- | --- |
| Windows images of the catalog, driver packs, Microsoft Update Catalog drivers and firmware | `Foundry\Downloads\`, published by devices |
| [Custom Windows images](customization/custom-windows-images.md), [custom driver sets](customization/custom-drivers.md) and [post-installation](customization/post-installation.md) content you imported | `Foundry\Cache\`, published by Foundry OSD when it creates media |

Foundry Deploy checks the hash of every file it reads from a share. A file that is incomplete or does not match is ignored, and the device downloads it from the Internet. Foundry Connect and Foundry Deploy themselves are not taken from the share.

A device that matches no scope, or reaches none of its shares, does not use the cache and downloads from the Internet. The deployment goes on in both cases.

## Before you start

- An SMB share for each site, written `\\server\share`. Foundry creates its `Foundry` folder at the root of the share.
- An account that can read, write and delete in that share, both in the share permissions and in the folder permissions. Use an account dedicated to this cache: anyone who can start the media and knows the Deployment password can use it.
- **Password protection** turned on in [General](general.md#password-protection) when a share has an account.
- The devices reach the file server on port 445 from Windows PE: see [Network endpoints](../reference/network-endpoints.md#target-device-in-windows-pe).
- The PC that runs Foundry OSD reaches the shares too, to publish imported content and to measure or empty them.

A share without an account is read without credentials and devices never write to it. Use one only when your file server allows reading without credentials, and fill it from Foundry OSD or from devices of another scope.

## Turn on the network cache

1. In the navigation pane, under **General**, open **Network cache**.
2. Select **Enable** at the top of the page.
3. Add the accounts, then the shares, then the IP scopes, as described below. Each change is saved at once.
4. If the page shows "Shares with an account require Deploy password protection, because a PXE boot image can be downloaded by anyone on the network. Turn it on in General.", turn on **Password protection** in [General](general.md#password-protection).

Select **Disable** to stop using the cache. The accounts, shares and scopes are kept for later.

### Accounts

Under **Accounts**, select **Add**. Type the **User name**, such as `DOMAIN\user` or `user@domain`, then the **Password** twice, and select **Add**.

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

3. Under **Shares**, check the shares of this scope and order them with the arrows. Devices try the checked shares from top to bottom, then download from the Internet.
4. Select **Add**.

When a device matches several scopes, the most specific one wins: the scope with the fewest addresses. Between two scopes of the same size, the one higher in the table wins.

## Imported content

Each time Foundry OSD creates an ISO, a USB drive or [PXE files](media/pxe-deployment.md), it also publishes the custom Windows images, custom driver sets and post-installation content of the media to every share of the page. The progress dialog shows "Publishing imported content to the network cache." at the end. Foundry OSD opens each share with its account, or with your Windows account when the share has none.

A share that cannot be reached or written does not make the media fail. The result then adds "The imported content could not be published to these network cache shares:", followed by the shares. Check them, then create the media again.

On the device, Foundry Deploy offers the custom images and driver sets found on the media first, then those of its share, and takes post-installation content the same way. A device started from the network therefore gets the imported content of the configuration, as long as it reaches its share: see [What a PXE start delivers](media/pxe-deployment.md#what-a-pxe-start-delivers).

## Space used and emptying

The **Space used** column shows, for each share, the size of the downloads published by devices and of the imported content published by Foundry OSD. Select **Refresh** to measure all the shares. A share that cannot be opened shows **Cannot be reached**.

To free space on a share:

1. Select **Refresh**, then select the share.
2. Select **Empty**, then **Empty** in the confirmation dialog.

Foundry OSD deletes every download of the share and the imported content that the current configuration no longer uses. Content that the configuration uses is kept. Media created from an older configuration then loses its imported content on this share; create it again if you still use it.

Files in use, changed since they were published, or not written by Foundry are kept, and a dialog says so. Devices download again from the Internet what was deleted, and publish it again.

## Several devices at the same time

Devices never wait for each other. When two devices need the same file and the share does not have it yet, both download it from the Internet; the first one to finish publishes it, and the other keeps its own copy. A device reads a file only once it is complete on the share.

A device writes a file under a temporary name and gives it its final name only once it is complete. A temporary file left by a device that stopped is deleted after 24 hours, by the next device that publishes the same file. **Empty** deletes them too.

## Check the result

1. Open [Start](media/README.md). In the **General** group, the **Network cache** row shows the number of shares and IP scopes. It must not show **Needs attention**.
2. Create the media and deploy a test device whose address is in one of the scopes.
3. In the Foundry Deploy log, look for "Network cache connected", followed by the share and whether the device can write there: see [Logs and support information](../troubleshooting/logs-and-support.md).
4. Deploy a second device in the same scope. Its Windows image and driver pack are read from the share instead of the Internet.

## If devices do not use the cache

The Foundry Deploy log says why a device did not use a share:

| Log line | Check |
| --- | --- |
| "Network cache not used. Outcome=NoMatchingScope" | None of the IP scopes contains an address of the device. Add its subnet or range. |
| "Network cache share connection failed", with a `Win32Error` | The path, the user name and the password of the account, and that the account can open the share. |
| "Network cache share connection timed out" or "Network cache share did not answer" | That the file server is up and that the device reaches it on port 445. |
| "Network cache connected", with `CanWrite=False` | The share is read, but the device does not publish: give the account the right to write and delete in the share and in its folder. |
| "Network cache unavailable; deployment continues without it." | None of the shares of the scope could be used. The lines above it name each share and the reason. |

## Related

- [General](general.md)
- [Deploy with PXE](media/pxe-deployment.md)
- [Configuration sharing](configuration-sharing.md)
- [Security and credentials](../reference/security-and-credentials.md)
