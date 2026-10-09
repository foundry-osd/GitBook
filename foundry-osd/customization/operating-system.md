# OS selection

**OS selection** limits the Windows versions, languages, license channels and editions that Foundry Deploy offers the technician, and chooses which ones are selected by default. Leave the page off to offer everything the Foundry catalog contains.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-customization-operating-system-01-selection.png`
- **Capture:** Show the OS selection page switched to Enabled, with the Windows version group expanded (26H2, 25H2 and 24H2 check boxes, Preselected Windows version, Preselected Windows update level) and the Windows edition group expanded with two editions ticked.
{% endhint %}

## Configure the Windows choices

1. Open **Customization > OS selection**.
2. Turn the switch at the top right to **Enabled**. Until you do, every control is unavailable and the media carries no restriction.
3. Expand a group and tick the values the technician may choose.
4. In the **Preselected ...** box of the same group, choose the value selected by default, or keep **Automatic**.
5. Create or update the deployment media. On **Start**, **OS selection** then shows **Configured**.

| Group | Allowed values | Default value |
| --- | --- | --- |
| **Windows version** | **Available Windows versions**: 26H2, 25H2, 24H2 | **Preselected Windows version** and **Preselected Windows update level** |
| **Windows language** | **Available Windows languages** | **Preselected Windows language** |
| **Windows license channel** | **Available Windows license channels**: Retail, Volume | **Preselected Windows license channel** |
| **Windows edition** | **Available Windows editions**: Home, Home N, Home Single Language, Home China, Education, Education N, Pro, Pro N, Enterprise, Enterprise N | **Preselected Windows edition** |

Three rules apply to every group:

- **An empty list means all.** A list with nothing ticked offers every supported value.
- **A single value forces the default.** When exactly one value is ticked, it becomes the preselected value and the **Preselected ...** box is locked. Foundry Deploy locks that list too.
- **Automatic** leaves the choice to Foundry: version 26H2, edition Pro, license channel Retail.

### Preselected Windows update level

Each Windows version is published as several monthly builds. **Preselected Windows update level** chooses the build selected by default: **Latest available update**, **1 update earlier**, and so on up to **11 updates earlier**. If the catalog holds fewer builds than you asked for, the oldest one is selected. The technician can still pick another build.

### Editions and license channels depend on each other

Home editions exist only in the Retail channel and Enterprise editions only in the Volume channel. Pro and Education exist in both. Foundry OSD keeps the two lists consistent as you tick editions:

- A channel that none of the ticked editions supports is cleared and locked.
- A channel that a ticked edition requires is locked. If you had ticked a channel yourself, Foundry OSD also ticks the required one: ticking **Enterprise** adds **Volume**.

To change a locked channel, change the ticked editions first.

## What the technician sees

On the **Operating system** step of Foundry Deploy, each list contains only the values you allowed, with your preselected value selected.

A restriction is dropped when it cannot be met. If none of the allowed values of a list exists in the catalog for the device, for example an allowed version that is no longer published or a language missing for the selected build, Foundry Deploy offers every available value for that list and records a warning in its log. This fallback applies in the same way to versions, languages, editions and license channels. See [Select Windows](../../foundry-deploy/operating-system.md).

## Limits

- This page restricts the images of the Foundry catalog only. It does not filter [Custom Windows images](custom-windows-images.md).
- Foundry deploys Windows 11 24H2, 25H2 and 26H2. See [Supported versions](../../reference/supported-versions.md).
- The versions and editions ticked here also set the availability labels on [Optional features](optional-features.md).

## Related

- [Select Windows](../../foundry-deploy/operating-system.md)
- [Catalogs](../../reference/catalog.md)
- [Windows deployment troubleshooting](../../troubleshooting/deployment.md)
