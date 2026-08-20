# Create deployment media

The Start page validates configuration and creates or updates Foundry deployment media.

## Review readiness

Resolve every blocking readiness item before starting. Checks cover Windows ADK and Windows PE, architecture, language, boot-image source, output paths, USB target, media options, drivers, networking, runtime configuration, secrets, customization, and Windows Autopilot.

<!-- SCREENSHOT_PENDING: foundry-osd-media-01-readiness-overview.png | Show the Start page readiness groups before media creation. -->

## Choose an operation

- [Create an ISO](create-iso.md)
- [Create a USB drive](create-usb.md)
- [Update a USB drive](update-usb.md)

During creation, Foundry reports workspace preparation, driver resolution, image customization, language and component processing, runtime payload provisioning, media creation, verification, and cleanup.
