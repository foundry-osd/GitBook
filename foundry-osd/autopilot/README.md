# Windows Autopilot

Foundry can prepare a device for Windows Autopilot in three ways: copy an Autopilot profile into the installed Windows, or register the device's [hardware hash](../../reference/glossary.md) in your tenant, either with nobody signing in or with the technician signing in. Deployment media uses one of these modes at a time.

| I want to ... | Go to |
| --- | --- |
| Give the device an Autopilot profile without registering it in my tenant | [JSON profile](json-profile.md) |
| Register every device during the deployment, with nobody signing in | [Zero-touch hardware hash upload](zero-touch-hardware-hash.md) |
| Let the technician register the device with their own account | [Interactive hardware hash upload](interactive-hardware-hash.md) |
| Know what the technician sees and does | [Windows Autopilot step](../../foundry-deploy/autopilot.md) |
| Understand a message or a device that is not registered | [Windows Autopilot troubleshooting](../../troubleshooting/autopilot.md) |

## Compare the modes

| | JSON profile | Zero-touch | Interactive |
| --- | --- | --- | --- |
| What Foundry does | Copies the profile file into the installed Windows. The device is not registered. | Uploads the hardware hash to your tenant | Uploads the hardware hash to your tenant |
| When | In Windows PE, at the end of the deployment | In Windows PE, at the end of the deployment | In Windows setup, after the restart |
| Who signs in on the device | Nobody | Nobody | The technician, with a code typed on another device |
| What you prepare | One or more profiles | A tenant connection, a certificate and the ADK Deployment Tools | Nothing: you only enable the mode |
| What the media carries | Every profile in the list | The certificate and its password | No tenant data and no secret |
| Step in the Foundry Deploy wizard | **Autopilot**: choose the profile | **Autopilot**: choose the group tag | No wizard step |
| Group tag | Not used | A tag already used in the tenant | A tag already used in the tenant, or a new one |
| **Upload computer name to Autopilot** | Not available | Available | Available |

## Rules for the three modes

- **One mode at a time.** Each page has an **Enable** button in its header, and the rest of the page stays unavailable until you select it. Enabling a mode while another one is active opens **Change provisioning mode?**; **Change mode** disables the other mode. **Disable** keeps what you configured on the page.
- Windows Autopilot and [Domain Join](../domain-join/README.md) exclude each other: enabling one asks you to confirm before it replaces the other.
- **No additional local accounts.** Media creation is blocked while a Windows Autopilot mode is enabled and [OOBE](../customization/oobe.md) has additional local accounts.
- **Custom answer files.** With **JSON profile** or **Interactive**, Foundry Deploy refuses an answer file that contains settings known to prevent Autopilot enrollment. With **Zero-Touch** it accepts the file and warns. See [What a custom answer file overrides](../customization/unattend.md#what-a-custom-answer-file-overrides).
- **The mode is part of the media.** Create or update the deployment media after any change on these pages.

Deploy one test device before you hand media to technicians. Foundry OSD checks what it holds on the workstation. It cannot check what only happens on the device: the network path to Microsoft, the technician's sign-in, or the profile your tenant assigns once the device is registered.
