# Machine naming

Use Machine naming to define how a computer name is selected during deployment.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-customization-machine-naming-01-configuration.png" alt="Foundry OSD machine naming configuration">
  <figcaption>Choose a naming mode and configure the ordered components used to build the computer name.</figcaption>
</figure>

## Naming requirements

Use 1–15 ASCII characters containing letters, numbers, or hyphens. Windows Setup rejects names containing only digits. Underscores are not supported by Foundry's naming rules.

For a numeric serial number, add fixed text such as `PC-` and limit the serial component to 12 characters so the result fits the 15-character limit. Truncation can remove the letters from an otherwise alphanumeric serial number, so check the complete result. Foundry rejects numeric-only final names before deployment confirmation.

{% hint style="warning" %}
Choose a naming method that prevents duplicate names and matches directory, inventory, and device-management requirements.
{% endhint %}

## Configure naming

1. Open **Customization > Machine naming**.
2. Enable machine naming.
3. Select **Manual** to let the deployment operator enter the complete name. You can optionally provide an initial value.
4. Select **Composed** to build the name from ordered components. Each component type can be used once:
   - Fixed text
   - Serial number
   - Manufacturer
   - Model
   - Asset tag
   - System UUID
   - Random text
5. Configure each component:
   - Enter the value for **Fixed text**.
   - For **Random text**, choose a length from 1 to 15 characters.
   - For device-data components, choose a maximum length from 1 to 15 characters and whether truncation keeps characters from the left or right. **Serial number** keeps characters from the right by default.
6. Use the arrow buttons to reorder components or the delete button to remove one. **Add component** becomes unavailable when no unused component can fit within the remaining budget.
7. Select either **No separator** or **Hyphen**, then choose whether to preserve, uppercase, or lowercase letters.
8. Keep the configured maximum within the 15-character budget shown on the page. Separators count toward this limit.
9. Decide whether the deployment operator can edit the complete generated name.
10. Return to **Start** and confirm customization readiness.

## How Foundry Deploy selects the name

The name shown in the Foundry Deploy wizard depends on the Machine naming configuration:

| Configuration | Name shown in the wizard |
| --- | --- |
| **Composed** | The name generated from the configured components and the device's hardware values. |
| **Manual** with an initial value | The configured initial value. |
| **Manual** without an initial value | The first available fallback name described below. |
| Machine naming disabled or not configured | The first available fallback name described below. |

When a fallback is required, Foundry Deploy uses the first valid name available in this order:

1. The computer name from the Windows installation already present on the device.
2. The current WinPE computer name, which is usually similar to `MININT-123ABC`.
3. `PC` if neither previous name is available or valid.

The name remains editable in the wizard unless **Composed** mode is configured to prevent editing. When editing is allowed, the operator can replace the entire generated name with any name that meets the length and character requirements above. The replacement does not have to match the configured components, casing, or separators.

If a composed name cannot be generated because a required hardware value is unavailable or contains a known firmware placeholder, Foundry Deploy displays an error without selecting a fallback name. When editing is allowed, entering a valid complete name clears the naming error and allows deployment to continue. When the name is locked, the error blocks deployment; correct the device data or update the naming configuration and recreate or update the media.

The preview uses representative values. Foundry Deploy resolves actual hardware values at deployment startup and applies casing and separators to the same component rules used in the preview. For random text, a random value is generated with the configured length during deployment startup.

## Upload the computer name to Autopilot

Enable **Upload computer name to Autopilot** to assign the final computer name confirmed in Foundry Deploy to the Windows Autopilot device record. This works with both **Manual** and **Composed** naming, including any changes made by the deployment operator. The uploaded value is the same final name used for Windows setup; the Foundry OSD preview and initial value are not uploaded separately.

1. Enable machine naming on **Customization > Machine naming**.
2. Enable either [Zero-touch hardware hash upload](../autopilot/zero-touch-hardware-hash.md) or [Interactive hardware hash upload](../autopilot/interactive-hardware-hash.md) under **Windows Autopilot**.
3. Return to **Machine naming** and turn on the switch in the **Upload computer name to Autopilot** card.
4. Create or update the deployment media to include the setting.

The switch is off by default. It is available only while machine naming and one of these hardware hash upload methods are enabled. A JSON profile alone does not support name upload. Configure this option in Foundry OSD; Foundry Deploy and the OOBE registration assistant have no separate switch.

Foundry assigns the name after the device becomes visible in Autopilot. Zero-touch upload performs this step during deployment in WinPE. Interactive upload carries the confirmed name into the OOBE assistant, which assigns it after registration. The option also applies to an existing Autopilot registration; when it is off, Foundry leaves that record's assigned name unchanged.

When a [custom answer file](unattend.md) is selected, Foundry does not manage the final Windows computer name and skips the Autopilot name assignment with an explanation in the deployment logs. If name assignment fails, review the reported Autopilot result and logs before handing over the device; a successful hardware hash upload alone does not confirm that the name was assigned.

{% hint style="info" %}
Autopilot assigned computer names apply to Microsoft Entra join scenarios. They do not control naming for Microsoft Entra hybrid join. See the [AssignedComputerName documentation](https://www.powershellgallery.com/packages/Get-WindowsAutoPilotInfo/3.8/Content/Get-WindowsAutopilotInfo.ps1).
{% endhint %}
