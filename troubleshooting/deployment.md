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

During **Checking cache...** for a Windows image or OEM driver pack, the verification percentage and bytes processed show how much of the cached file has been checked. If verification takes longer than expected, allow it to finish. Large files and slower USB drives take longer to read. When a catalog hash is available, Deploy checks the file contents before reusing them, even if the file was used successfully before.

A cached file that fails verification is downloaded again automatically. The display switches from cache verification to the replacement download, which has its own progress. If the replacement also fails with a hash verification error, collect the logs and check the download source, deployment storage, and network before retrying. Files without a catalog hash cannot receive this integrity check.

Artifact downloads stop if no data is transferred for two minutes. A connection error can stop a request sooner. There is no fixed total duration limit: a slow download can continue while data is arriving. Check the connection and available storage before retrying a timeout. You can also [cancel deployment](../foundry-deploy/review-and-deploy.md#cancel-deployment) while it is running.

## Checks before disk preparation fail

If Foundry stops before **Prepare target disk**, the target has not been erased by that deployment attempt. Image download and inspection run before or after this step depending on the available storage route; see [the deployment timeline](../foundry-deploy/review-and-deploy.md#follow-progress).

Record the failed step and follow the reported action:

- For source-access or image-download errors, check the network and selected catalog entry.
- For an unavailable edition, select another Windows image containing the required edition.
- For insufficient space, choose a larger target or make room on the deployment USB cache. Do not delete files from the intended target as a workaround for an image or network error.
- For a cache-location error, check that the cache is available on separate storage and restart Foundry Deploy after correcting the connection.

With ISO or USB overflow to target storage, some image checks finish after disk preparation. A successful source-access check does not guarantee that the complete download or later image verification will succeed. See [checks before disk preparation](../foundry-deploy/review-and-deploy.md#checks-before-disk-preparation).

## Deployment stops with an error

1. Record the failed step exactly as displayed.
2. Capture the complete error details without exposing secrets.
3. Collect logs before rebooting or starting another deployment.
4. Correct the underlying cause.
5. Restart the workflow only after verifying the target and deployment inputs again.

There is no rollback or resume operation. A failure after disk preparation can leave the target partially deployed and unable to boot, and a retry starts again from the beginning.

## Custom image failures (unreleased)

For [custom images](../foundry-osd/customization/custom-windows-images.md), check that the complete ISO or USB remains available and that the chosen numeric index exists. Do not substitute a same-name manual WIM for a missing managed preference. Changed files, manifest mismatches, or a source disk chosen as the deployment target must be resolved before deployment. DISM apply or servicing failures can still occur after target preparation; review logs and validate the WIM and customizations on a test target.
