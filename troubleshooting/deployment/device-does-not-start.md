# The device does not start

Foundry Deploy showed **Deployment complete**, the device restarted, and Windows does not start. The disk was erased and Windows was written to it.

| What you see | Go to |
| --- | --- |
| Foundry starts again, the device starts PXE, or the firmware boot menu opens | [Returns to the deployment media](#returns-to-the-media) |
| No boot device, a restart loop, or a stop error | [Does not start Windows](#does-not-start-windows) |
| Windows starts and a Foundry console stops or shows a failure | [After the restart troubleshooting](../after-the-restart.md) |

## After the restart, the device returns to the deployment media <a href="#returns-to-the-media" id="returns-to-the-media"></a>

**Where:** after **Deployment complete**. The device starts Foundry again, starts PXE, or opens the firmware boot menu.

**Cause:**

- The deployment media is still connected and comes first in the firmware boot order.
- The firmware refused to put **Windows Boot Manager** first. Foundry requests it at the end of **Configure Windows boot**, and a refusal does not fail the deployment. The log then contains "The firmware boot order could not be fully updated. The device may start from another boot device after the restart."

**Fix:**

1. Do not select **Start deployment**: that would lead to a second erase.
2. Remove the USB drive or the ISO and restart.
3. If the device still does not start Windows, open the firmware boot menu and select **Windows Boot Manager**.
4. Put **Windows Boot Manager** first in the firmware boot order.

**Collect:** the deployment log from the installed Windows, `C:\Windows\Temp\Foundry\Logs\Deployment`. It lists the firmware boot entries found at the end of the deployment.

## After the restart, the device does not start Windows <a href="#does-not-start-windows" id="does-not-start-windows"></a>

**Where:** after **Deployment complete**: no boot device, a restart loop, or a stop error such as INACCESSIBLE_BOOT_DEVICE.

**Cause:**

- The firmware starts in legacy BIOS (CSM) mode. Foundry always creates a GPT disk that starts in UEFI mode only.
- Windows has no driver for the storage controller. This happens when **Driver source** was **None**, when Microsoft Update Catalog returned nothing, or when the pack is an installer that runs only once Windows has started (Lenovo `.exe`, Surface `.msi`).
- The firmware boot order was not updated: see [the previous entry](#returns-to-the-media).

Foundry erases the whole disk, including encrypted volumes. It does not clear the TPM, remove boot entries left by the previous installation or change the Secure Boot settings.

**Fix:**

1. In the firmware setup, set the boot mode to UEFI and disable legacy or CSM boot.
2. Select **Windows Boot Manager** in the firmware boot menu.
3. Deploy again with a manufacturer **Driver source**, or change the storage controller mode to one Windows supports without an extra driver.
4. Remove old boot entries in the firmware setup if several entries named **Windows Boot Manager** exist.

**Collect:** the firmware boot mode, the exact stop code, and the deployment logs left on the target disk: see [Collect logs from a device that does not start](../logs-and-support.md#collect-logs-from-a-device-that-does-not-start).
