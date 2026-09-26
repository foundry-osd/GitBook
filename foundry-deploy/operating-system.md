# Select Windows

The Operating system step selects the Windows media applied to the target.

## Make a selection

1. Select the Windows release.
2. Select available media for that release.
3. Select the language.
4. Select the edition.
5. Select the license channel when more than one is available.

<figure>
  <img src="../.gitbook/assets/foundry-deploy-operating-system-01-selection.png" alt="Foundry Deploy Windows release, language, edition, and license selection">
  <figcaption>Select a compatible Windows release, language, edition, and license channel.</figcaption>
</figure>

Available releases, languages, editions, architectures, and license channels come from the current [operating-system catalog](../reference/catalog.md) and any restrictions configured during media authoring. The catalog can update independently of the application. Confirm that the selection matches licensing and application compatibility requirements.

## Custom images

Media authored with [Custom Windows images](../foundry-osd/customization/custom-windows-images.md) enabled can offer a custom image source alongside the catalog. Choose the image and its exact numeric WIM index. Identical edition names do not identify the same index. The authoring profile can select the initial source and a preferred image/index; these remain separate choices.

Below the image and index selectors, read-only fields show the selected index's version (including its revision), edition, architecture, and language. These values update when you select a different index.

Foundry Deploy discovers images automatically at startup and when you choose **Custom image**. If you add a manual WIM to the USB cache during the session, switch to **Windows catalog**, then back to **Custom image** to discover it.

The profile's enabled customizations apply to custom images. Catalog release restrictions do not determine which custom WIM can be used. Keep the source media connected throughout deployment, and resolve missing explicit preferences rather than expecting an automatic replacement. Internet access and Foundry Connect are still required.
