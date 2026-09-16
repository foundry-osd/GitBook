# Windows deployment troubleshooting

## Computer name is invalid

Use 1–15 ASCII letters, numbers, or hyphens, and avoid a name containing only digits. A numeric serial number, or truncation that removes its letters, can produce a name Windows Setup rejects. Use fixed text such as `PC-` with at most 12 serial characters.

In the composed naming workflow, a missing hardware value or firmware placeholder also prevents generation. If editing is allowed, enter a valid complete name. Otherwise, correct the naming configuration and refresh the media. See [machine naming](../foundry-osd/customization/machine-naming.md) for naming requirements and editing options.

## Target disk is unavailable

- Confirm the storage controller is supported in Windows PE.
- Add the required storage driver to deployment media.
- Disconnect removable disks that can confuse target selection.
- Confirm that the disk is not connected over USB and is not marked as a system, boot, read-only, or offline disk.

## Windows selection is empty

- Confirm Internet and catalog access.
- Confirm the media authoring configuration permits the required release and architecture.
- Review [Catalogs](../reference/catalog.md).

## Driver pack is unavailable

- Confirm manufacturer and model identification.
- Confirm the selected Windows release and architecture.
- Check whether the catalog marks the package as legacy or targets a different model family.

## Download fails

Record the current deployment step, source type, and complete error. Confirm DNS, proxy, firewall, available storage, and system time before retrying.

For an HTTPS certificate error, check the device clock and certificate trust in Windows PE. If your network inspects HTTPS traffic, ask your administrator to provide the required trusted certificates or a network path that does not replace the server certificate.

If **Checking cache...** takes longer than expected, allow the file verification to finish. Large Windows images and slower USB drives take longer to read. When a catalog hash is available, Deploy checks the file contents before reusing them, even if the file was used successfully before.

A cached file that fails verification is downloaded again automatically. If the replacement also fails with a hash verification error, collect the logs and check the download source, deployment storage, and network before retrying. Files without a catalog hash cannot receive this integrity check.

## Deployment stops with an error

1. Record the failed step exactly as displayed.
2. Capture the complete error details without exposing secrets.
3. Collect logs before rebooting or starting another deployment.
4. Correct the underlying cause.
5. Restart the workflow only after verifying the target and deployment inputs again.

There is no rollback or resume operation. A failure after disk preparation can leave the target partially deployed and unable to boot, and a retry starts again from the beginning.
