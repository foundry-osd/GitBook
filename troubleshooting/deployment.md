# Windows deployment troubleshooting

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

Use only a trusted, controlled deployment network. Foundry Deploy does not validate HTTPS server certificates in Windows PE. A file hash is checked only when the selected catalog provides one, and the catalog is obtained through the same network path.

## Deployment stops with an error

1. Record the failed step exactly as displayed.
2. Capture the complete error details without exposing secrets.
3. Collect logs before rebooting or starting another deployment.
4. Correct the underlying cause.
5. Restart the workflow only after verifying the target and deployment inputs again.

There is no rollback or resume operation. A failure after disk preparation can leave the target partially deployed and unable to boot, and a retry starts again from the beginning.
