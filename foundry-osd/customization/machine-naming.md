# Machine naming

Use Machine naming to define how a computer name is selected during deployment.

## Naming requirements

Foundry Deploy validates names as 1–15 characters containing letters, numbers, or hyphens. Underscores are not available because Windows computer names that use them are not DNS compatible.

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
5. For device-data components, choose a maximum length from 1 to 15 characters and whether truncation keeps characters from the left or right. Serial numbers keep characters from the right by default.
6. Reorder the components, select no separator or a hyphen, and choose whether to preserve, uppercase, or lowercase letters.
7. Keep the configured maximum within the 15-character budget shown on the page. Separators count toward this limit.
8. Decide whether the deployment operator can edit the complete generated name.
9. Return to **Start** and confirm customization readiness.

The preview uses representative hardware values. Foundry Deploy resolves the actual device values at startup and generates a random component once per deployment session. Deployment is blocked when a required hardware value is missing, blank, or a known placeholder.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-customization-machine-naming-01-configuration.png" alt="Foundry OSD machine naming configuration">
  <figcaption>Choose a naming mode and configure the ordered components used to build the computer name.</figcaption>
</figure>
