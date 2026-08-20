# Logs and support information

Collect evidence before rebooting, recreating media, or starting another deployment.

## Information to record

- Foundry application and media version.
- Device manufacturer and model.
- Current application: Foundry OSD, Foundry Connect, or Foundry Deploy.
- Current or failed workflow stage.
- Complete error message.
- Network state and connection type.
- Selected Windows release, edition, language, and architecture.
- Selected driver pack.
- Autopilot method, without credentials or tenant secrets.

## Windows PE log location

Foundry Connect and Foundry Deploy initially write logs under:

```text
X:\Foundry\Logs
```

## Applied Windows log location

After Foundry prepares the target Windows installation, deployment logs are rebound to:

```text
<target-drive>:\Windows\Temp\Foundry\Logs
```

After the deployed operating system starts, this is normally:

```text
C:\Windows\Temp\Foundry\Logs
```

Relevant subdirectories include:

```text
PreOobe
AutopilotHash
AutopilotRegistration
```

If the first-boot runner does not start, also collect:

```text
C:\Windows\Panther\UnattendGC\Setupact.log
```

{% hint style="warning" %}
Review collected files before sharing them. Remove credentials, tokens, certificates, hardware hashes, tenant identifiers, network secrets, and other sensitive information.
{% endhint %}

## Open a support issue

Provide reproduction steps, expected result, actual result, failed stage, sanitized logs, and whether the problem reproduces on newly created media.
