# Zero-touch hardware hash upload

Zero-touch upload registers a device without requiring a technician to sign in during deployment.

## Prerequisites

- Microsoft Entra tenant information.
- An application registration approved for the deployment workflow.
- Required Microsoft Graph application permissions and administrator consent.
- A plan to create a Foundry-managed certificate after connecting the tenant, or an approved matching PFX and private key for boot media.
- A credential-rotation process that covers certificate expiration.

## Configure zero-touch upload

1. Open **Windows Autopilot > Zero-touch hardware hash upload**.
2. Select **Connect tenant** and complete the Microsoft sign-in flow.
3. Allow Foundry OSD to create or adopt the tenant application registration used for upload.
4. Review application registration, Microsoft Graph permissions, administrator consent, and service-principal readiness.
5. Create a Foundry-managed certificate or select the approved matching PFX and provide its password for boot-media provisioning.
6. Configure the group tag when required by the organization.
7. Return to **Start** and confirm Autopilot readiness.

{% hint style="warning" %}
**Screenshot required**

- **File:** `foundry-osd-autopilot-zero-touch-01-readiness.png`
- **Capture:** Show zero-touch prerequisite validation with sanitized tenant and application values.
{% endhint %}

{% hint style="danger" %}
Protect the certificate private key and generated media. Revoke or rotate the credential if its confidentiality is uncertain.
{% endhint %}

## During deployment

Foundry Deploy captures the hardware hash and uses the configured application identity to upload it.

{% hint style="warning" %}
Windows deployment can succeed even when Microsoft Graph upload or registration polling fails. Review the Autopilot result and verify the device record in the tenant before handoff.
{% endhint %}
