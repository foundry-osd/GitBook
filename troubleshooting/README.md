# Troubleshooting

Start with the stage where the workflow stopped.

| Stage | Start here |
| --- | --- |
| ADK, Windows PE, ISO, or USB creation | [Media creation](media-creation.md) |
| Ethernet, Wi-Fi, DHCP, or Foundry Connect | [Network and Foundry Connect](network.md) |
| Target, Windows, drivers, or deployment execution | [Windows deployment](deployment.md) |
| Profile staging or hardware hash upload | [Windows Autopilot](autopilot.md) |
| AD joining, OU placement, membership or domain credential cleanup (unreleased) | [Domain Join](domain-join.md) |

Before changing configuration, record the visible status, failed step, complete error message, device model, media version, and selected workflow. Then collect [logs and support information](logs-and-support.md).

For unreleased domain work, collect independent phase states and safe numeric error codes from [domain outcome evidence](logs-and-support.md#domain-join-evidence-unreleased), without the credential payload or account/password.
