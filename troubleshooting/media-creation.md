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

## Readiness remains blocked

Open each reported readiness group and correct the specific missing or invalid value. Common blockers include output paths, Windows PE language, architecture, boot-image source, USB target, runtime configuration, secrets, and incompatible customization.

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
