# Media creation troubleshooting

## ADK or Windows PE remains unavailable

**Evidence to collect**

- Detected ADK version.
- Detected Windows PE Add-on status.
- Installer result shown by Foundry OSD.
- Whether Foundry is running as administrator.

**Actions**

1. Confirm Internet access and administrative permissions.
2. Complete any other Windows Installer operation.
3. Install Windows ADK `10.1.26100.2454` before Windows PE Add-on `10.1.26100.2454`.
4. Restart Foundry OSD and refresh detection.

## Foundry OSD cannot reach online services

1. Open **Settings > Proxy**.
2. Choose **Use Windows settings** when the workstation already receives its proxy configuration from Windows.
3. Otherwise configure the approved manual proxy address, port, bypass rules, and authentication method.
4. Select **Test connection**. Testing uses the displayed values without saving them.
5. After a successful test, select **Apply** to save the configuration for new Foundry OSD connections.

If authentication fails, verify whether the proxy expects the current Windows identity or a dedicated username, password, and optional domain. Explicit credentials are stored in Windows Credential Manager.

{% hint style="warning" %}
Foundry OSD proxy settings do not configure deployment media, Foundry Connect, or Foundry Deploy. A media-creation download can work in Foundry OSD while the same network blocks runtime catalogs, downloads, or Microsoft services. Runtime deployment requires direct access, a transparent network proxy, or a separately managed Windows PE network configuration.
{% endhint %}

## Readiness remains blocked

Open each reported readiness group and correct the specific missing or invalid value. Common blockers include output paths, Windows PE language, architecture, boot-image source, USB target, runtime configuration, secrets, and incompatible customization.

## Download times out

Windows source, driver package, and runtime downloads stop after two minutes without data transfer. A connection error can stop a request sooner. There is no fixed total duration limit while data continues to arrive. Check the workstation's network connection, proxy settings and available storage, then retry media creation. See [cancelling media creation](../foundry-osd/media/README.md#cancel-media-creation) if you want to stop a running operation.

## A mounted image cannot be cleaned up

If saving or discarding a mounted Windows image fails, Foundry attempts cleanup again after the previous command has finished. The original media-creation error remains available in the logs. Foundry keeps the temporary workspace when an image is still mounted or its mount state cannot be checked safely. If a cleanup command times out or its completion is uncertain, Foundry preserves that workspace across restarts instead of starting another cleanup attempt. Retained workspaces continue to use disk space.

Close File Explorer windows, terminals and other tools using the mounted image, then retry media creation. Do not manually delete the retained workspace or change its file permissions while an image is mounted. If the error persists, collect the [logs and support details](logs-and-support.md) so the remaining mount can be checked before removing files.

## ISO creation fails

- Confirm free space in both the temporary workspace and output location.
- Confirm the output file is not open or locked.
- Confirm security software has not quarantined a required deployment tool.
- Record the failed operation before retrying.

## USB creation fails

{% hint style="danger" %}
Creating USB media formats and erases the selected disk. Reconfirm the target before every retry.
{% endhint %}

- Verify that the drive is still connected and writable.
- Close applications using the drive.
- Confirm that the selected layout supports the target firmware.
- Do not retry against a different disk until its identity is verified.
