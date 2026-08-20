# Windows Autopilot

Foundry supports three Autopilot workflows. Choose the method that matches the organization’s provisioning and security model.

## Choose a method

### JSON profile

Use [JSON profile](json-profile.md) to stage an existing Autopilot profile on the deployed device. This method does not upload the device hardware hash.

### Zero-touch hardware hash upload

Use [zero-touch hardware hash upload](zero-touch-hardware-hash.md) for unattended registration with a Microsoft Entra application registration and certificate.

### Interactive hardware hash upload

Use [interactive hardware hash upload](interactive-hardware-hash.md) when a technician should authenticate with a device code during deployment.

| Requirement | JSON profile | Zero-touch upload | Interactive upload |
| --- | --- | --- | --- |
| Local profile file | Required | No | No |
| Application registration | No | Required | No |
| Certificate | No | Required | No |
| Technician sign-in | No | No | Required |
| Hardware hash upload | No | Automatic | Interactive |

{% hint style="warning" %}
Autopilot configuration may include tenant information, certificates, and credentials. Follow the organization’s credential and media-handling policy.
{% endhint %}
