# AI components

Use AI components to control supported Windows AI-related components during deployment.

## Configure component actions

1. Open **Customization > AI components**.
2. Enable AI component customization. Foundry enables all available AI actions.
3. Disable any actions that are not approved for the deployment standard.
4. Return to **Start**.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-customization-ai-components-01-selection.png" alt="Foundry OSD AI component removal options">
  <figcaption>Select which supported AI-related components Foundry removes during deployment.</figcaption>
</figure>

This page does not report per-release compatibility. Test the deployed Windows image after every change because component availability and servicing behavior can change between Windows releases.

In the [Post-installation workflow](post-installation.md#execution-order), selected Copilot and AI Hub package removals run before custom actions and OOBE. Other AI changes may be applied earlier during deployment. Disabling custom Post-installation actions does not disable these selected removals.
