# Image conventions

This document defines how screenshots, diagrams, and image placeholders are stored and referenced in the Foundry OSD documentation.

## Asset location

Store all documentation images in the GitBook asset directory:

```text
.gitbook/assets/
```

Keep the directory flat. Use the filename to identify the product surface, page, sequence, subject, state, and optional theme.

## Filename format

```text
<surface>-<page>-<sequence>-<subject>[-<state>][-<theme>].<extension>
```

Use:

- Lowercase ASCII characters.
- Kebab-case words.
- Two-digit sequence numbers starting at `01` on each page.
- Stable names without dates or application versions.

Supported surface prefixes:

- `foundry-osd`
- `foundry-connect`
- `foundry-deploy`
- `shared`

Optional states:

- `default`
- `empty`
- `progress`
- `success`
- `warning`
- `error`

Optional themes:

- `light`
- `dark`

Examples:

```text
foundry-osd-adk-01-status-ready.png
foundry-osd-adk-02-install-button.png
foundry-osd-media-create-03-usb-warning.png
foundry-connect-network-readiness-02-wifi-error.png
foundry-deploy-operating-system-01-edition-selection.png
foundry-deploy-progress-02-download-error.png
shared-deployment-workflow.svg
```

## File formats

- Use PNG for application screenshots.
- Use SVG for diagrams, icons, and simple illustrations.
- Use WebP or JPEG only for photographic content.
- Use separate `light` and `dark` files only when the theme materially changes what the reader must identify.

## Screenshot placeholders

Add an invisible placeholder where a screenshot is expected but not yet available:

```html
<!-- SCREENSHOT_PENDING: foundry-osd-adk-02-install-button.png | Show the automatic ADK and WinPE Add-on installation button before installation. -->
```

The placeholder must include:

1. The final image filename.
2. A concise description of what the capture must show.

Replace the marker with the final image before publication:

```html
<figure>
  <img
    src="../.gitbook/assets/foundry-osd-adk-02-install-button.png"
    alt="Automatic ADK installation button on the Foundry OSD ADK page"
  >
  <figcaption>
    Install the supported ADK and Windows PE Add-on directly from Foundry OSD.
  </figcaption>
</figure>
```

Use the correct relative path for the page containing the image.

## When to add a screenshot

Add a screenshot when it clarifies:

- A control that may be difficult to locate.
- A destructive confirmation, such as USB formatting.
- A configuration screen containing several related options.
- An important readiness, progress, success, warning, or error state.
- A troubleshooting step that requires visual identification.

Do not add screenshots that merely repeat an obvious instruction or provide no additional context.

## Capture requirements

- Capture only the relevant application area.
- Use a consistent Windows display scale and application window size.
- Keep text and controls sharp and readable.
- Do not include unrelated desktop content or window chrome unless it provides necessary context.
- Use the default application theme unless a theme-specific capture is required.
- Add concise, descriptive alt text to every image.
- Add a caption when it explains the result, consequence, or context of the screen.

## Sensitive information

Screenshots must not expose:

- Usernames or email addresses.
- Tenant, subscription, or organization identifiers.
- Device identifiers, serial numbers, or hardware hashes.
- Wi-Fi credentials or network secrets.
- Certificates, private keys, client secrets, or access tokens.
- Internal URLs, IP addresses, or environment names that are not intended to be public.

Use sanitized demonstration data. Redact sensitive information before adding an image to the repository.

## Maintenance

- Replace an outdated image in place when its documentation purpose has not changed.
- Rename an image only when its page or purpose changes.
- Remove images that are no longer referenced.
- `SCREENSHOT_PENDING` markers are allowed while the GitBook site remains private or in draft review.
- Before public publication, replace every marker or explicitly approve the corresponding screenshot as non-blocking.
