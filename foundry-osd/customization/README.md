# Customization

The **Customization** pages of Foundry OSD decide which Windows the technician can install and what Foundry changes in it before the first sign-in. Every page is optional: configure only what your deployment standard requires.

| I want to ... | Go to |
| --- | --- |
| Limit the Windows versions, languages, editions and license channels the technician can choose | [OS selection](operating-system.md) |
| Deploy my own Windows image instead of, or next to, the images of the Foundry catalog | [Custom Windows images](custom-windows-images.md) |
| Use my own answer file for Windows Setup | [Unattend](unattend.md) |
| Build the computer name from device data, or prefill it | [Machine naming](machine-naming.md) |
| Skip setup pages, set privacy defaults and create local accounts | [OOBE](oobe.md) |
| Run scripts, commands and installers before the first sign-in | [Post-installation](post-installation.md) |
| Turn Windows optional features on or off | [Optional features](optional-features.md) |
| Remove built-in Windows apps | [AppX removals](appx-removals.md) |
| Remove Copilot and turn off Windows AI features | [AI components](ai-components.md) |

## How these pages work

Each page has a switch at the top right that reads **Enabled** or **Disabled**. Every page starts **Disabled**. While a page is **Disabled**, its controls are unavailable and it adds nothing to the deployment media.

Foundry OSD saves each change as you make it. A change reaches a device only after you create or update the deployment media.

## When each customization is applied

| Stage | What happens |
| --- | --- |
| In Foundry Deploy, before the disk is erased | OS selection and Custom Windows images decide which images are offered and preselected. Machine naming proposes the computer name. |
| In Windows PE, after Windows is applied to the disk | OOBE settings, AI policies and Optional features are written into the installed Windows. |
| At the first start of Windows, before the first sign-in | Copilot and AI Hub removal, then AppX removals, then Post-installation actions. |

## Check the status on Start

**Start** lists each customization page with a status.

| Status | Meaning |
| --- | --- |
| **Disabled** | The page is switched off. Optional features and AppX removals also show **Disabled** when they are on with nothing selected. |
| **Configured** | The page is on and its settings are valid. |
| **Needs attention** | The page is on but incomplete or invalid. Foundry OSD cannot create media until you fix the page or switch it off. |

Only Custom Windows images, Unattend, Machine naming, OOBE and Post-installation can show **Needs attention**. Open the page to see what is missing.

## Test before production

Deploy a test device of each hardware model you support before you update production media, and again after every change to these pages. Removing apps, features and AI components changes what later Windows updates and your own applications find on the device, and some settings fail only on real hardware or on a specific Windows edition.
