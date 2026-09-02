# Zero-touch hardware hash upload

Zero-touch upload registers a device without requiring a technician to sign in during deployment.

## Prerequisites

- Microsoft Entra tenant information.
- An application registration approved for the deployment workflow.
- Required Microsoft Graph application permissions and administrator consent.
- A plan to create a Foundry-managed certificate after connecting the tenant, or an approved password-protected PFX containing the certificate and private key.
- A credential-rotation process that covers certificate expiration.

## Configure zero-touch upload

1. Open **Windows Autopilot > Zero-touch hardware hash upload**.
2. Select **Connect tenant** and complete the Microsoft sign-in flow.
3. Allow Foundry OSD to create or adopt the tenant application registration used for upload.
4. Review application registration, Microsoft Graph permissions, administrator consent, and service-principal readiness.
5. Create a Foundry-managed certificate or select an approved password-protected PFX containing the certificate and private key, then provide its password.
6. Configure the group tag when required by the organization.
7. Return to **Start** and confirm Autopilot readiness.

<figure>
  <img src="../../.gitbook/assets/foundry-osd-autopilot-zero-touch-01-readiness.png" alt="Foundry OSD zero-touch hardware hash upload prerequisite validation">
  <figcaption>Confirm tenant, application, permission, and certificate readiness.</figcaption>
</figure>

{% hint style="danger" %}
Protect the certificate private key and generated media. Revoke or rotate the credential if its confidentiality is uncertain.
{% endhint %}

## Permissions and authentication

Foundry uses two authentication contexts while configuring and running zero-touch upload:

- During tenant setup in **Foundry OSD**, the signed-in administrator authorizes Foundry to configure the application registration and read the Autopilot configuration required by the workflow.
- During deployment, **Foundry Deploy** uses the certificate stored in the boot media to authenticate as the configured application. No technician sign-in is required.

The application used by Foundry Deploy requires the Microsoft Graph application permission `DeviceManagementServiceConfig.ReadWrite.All` with administrator consent. This permission allows the application to import the device identity and check the import result. Treat the application registration and its certificate as privileged deployment credentials.

Foundry OSD requests delegated `Application.ReadWrite.All` and `DeviceManagementServiceConfig.Read.All` permissions from the signed-in administrator while creating or reviewing the application registration and Autopilot configuration. The permissions available to the administrator remain subject to Microsoft Entra roles, Conditional Access, and tenant policy.

{% hint style="info" %}
Use a dedicated application registration for Foundry deployment media. Do not reuse a certificate or application registration that grants unrelated access.
{% endhint %}

## How credentials are protected

Foundry places the certificate PFX and its PFX password in the deployment configuration. Both values are encrypted with AES-256-GCM, which also detects whether the encrypted data has been modified.

Enable **Protected deployment** from [General configuration](../general.md) before creating the media.

Foundry generates a random 256-bit deployment key for each media-creation operation. When Protected deployment is enabled:

1. Foundry derives a key from the deployment password using PBKDF2-HMAC-SHA-256, 600,000 iterations, and a random 128-bit salt.
2. The password-derived key protects the random deployment key with AES-256-GCM.
3. The deployment key is not stored separately on the media.
4. Foundry Deploy asks for the password and unlocks the deployment key before it can use the certificate.

AES-GCM uses a unique 96-bit nonce and a 128-bit authentication tag for each encrypted value.

{% hint style="warning" %}
Protected deployment does not encrypt the entire ISO, USB drive, or Windows installation content. It protects access to Foundry Deploy and the deployment secrets stored in its configuration.
{% endhint %}

If Protected deployment is disabled, the PFX and its password remain encrypted, but the deployment key is stored on the media so that deployment can start without a password. Anyone who obtains the media must therefore be treated as having access to its embedded deployment credentials.

Use a unique password of at least 12 characters for deployment media. Foundry accepts passwords from 8 characters, but warns when fewer than 12 characters are used. The password is not stored on the media. If it is lost, recreate the media.

## Operational security

- Restrict physical and administrative access to generated ISO and USB media.
- Use a short-lived certificate where organizational policy permits it.
- Record the certificate expiration date and rotate the certificate before it expires.
- Revoke the certificate and recreate all affected media after loss, theft, or suspected copying.
- Delete obsolete ISO files and securely retire USB media when they are replaced.
- Do not attach deployment configuration, certificates, or unredacted logs to public support requests.
- Verify that Conditional Access, proxy, firewall, and Microsoft Graph policies allow the deployment workflow before distributing media.

## During deployment

Foundry Deploy captures the hardware hash and uses the configured application identity to upload it.

{% hint style="warning" %}
Windows deployment can succeed even when Microsoft Graph upload or registration polling fails. Review the Autopilot result and verify the device record in the tenant before handoff.
{% endhint %}
