# AppX removals

Use AppX removals to remove selected provisioned Windows application packages during deployment.

## Configure removal

1. Open **Customization > AppX removals**.
2. Enable AppX removals.
3. Select only applications approved for removal.
4. Review dependencies and organizational application requirements.
5. Return to **Start**.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-customization-appx-removals-01-selection.png" alt="Foundry OSD AppX removals profile and package selection">
  <figcaption>Select the AppX packages to remove during deployment.</figcaption>
</figure>

{% hint style="warning" %}
Removing provisioned applications can affect user experience, later servicing, and dependent workflows. Test every removal set against the target Windows release.
{% endhint %}

In the unreleased [Post-installation workflow](post-installation.md#execution-order), selected AppX removals run before custom actions and OOBE. They still run when custom Post-installation actions are disabled.
