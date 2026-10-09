# Glossary

Terms used in this documentation, in alphabetical order.

## 802.1X

A network access standard in which a device must authenticate, with a certificate or an account, before the Ethernet port or the Wi-Fi network lets it through.

## ADK

Windows Assessment and Deployment Kit, a free set of Microsoft tools. Foundry OSD uses its Deployment Tools and the Windows PE add-on to build deployment media.

## Administrator

In this documentation, the person who prepares deployment media in Foundry OSD on a workstation.

## Answer file

An XML file, often named `unattend.xml`, that gives Windows Setup its settings so that it does not ask for them. Foundry builds one from your settings; the **Unattend** page lets you supply your own.

## Boot image

The file `boot.wim` on the deployment media. It contains Windows PE and is what the target device loads when it starts from the media.

## Catalog

A list published by the Foundry project and read over the Internet: the Windows releases that can be downloaded, and the driver packs of each manufacturer.

## Configuration

The set of deployment options chosen in Foundry OSD. **Settings backup and sync** saves, exports and shares configurations.

## Deployment media

The ISO file or USB drive created by Foundry OSD, from which a target device starts.

## Deployment password

The password the technician types when Foundry Deploy starts, on media created with **Password protection** turned on.

## Distinguished name

The full path of an object in Active Directory, such as `OU=Workstations,DC=corp,DC=contoso,DC=com`. Foundry uses it to name an OU.

## Domain Join

Making the target device a member of an Active Directory domain.

## Driver pack

A package of drivers published by a manufacturer for one model or family of devices, installed with Windows during the deployment.

## Foundry Cache

The second volume of a USB drive created by Foundry OSD, labelled `Foundry Cache`. It keeps downloaded files, so that later deployments from the same drive do not download them again.

## Foundry Connect

The application that checks and sets up network access on the target device in Windows PE, before Foundry Deploy.

## Foundry Deploy

The application in which the technician chooses the disk, Windows and drivers, and which installs Windows on the target device.

## Foundry OSD

The desktop application, installed on the administrator workstation, that holds the deployment settings and creates the deployment media.

## Hardware hash

Data computed from the hardware of a device, which identifies it to Windows Autopilot. Uploading it to the tenant registers the device.

## Interactive

A method that asks the technician for something during the deployment, such as a sign-in or the join account. The opposite is zero-touch.

## Join account

The Active Directory account used to join a computer to the domain.

## OOBE

Out-of-box experience: the screens Windows shows at the first start, for region, keyboard, account and privacy choices.

## OU

Organizational unit: a container in Active Directory in which the computer account is placed.

## Password protection

The option of Foundry OSD that protects most secrets stored on the media with the Deployment password. The **General** page lists what is covered.

## PCA 2011 and PCA 2023

Two Microsoft certificates used to sign boot files for Secure Boot. PCA 2023 is the current one and the default in Foundry OSD.

## Post-installation

The step that runs in the installed Windows after the restart and before the first sign-in. It completes Foundry's own tasks, then runs the actions the administrator added on the **Post-installation** page.

## PXE

Preboot Execution Environment: a way for a device to start from the network instead of from a disk or a USB drive.

## Specialize pass

A phase of Windows Setup that runs once at the first start of the installed Windows, before OOBE. Foundry's post-installation step runs in it.

## SYSTEM

The built-in Windows account with full rights on the device. Post-installation actions run as SYSTEM, with no signed-in user.

## Target device

The computer on which Windows is being installed.

## Target disk

The disk of the target device selected in Foundry Deploy. It is erased and repartitioned.

## Technician

In this documentation, the person who starts the target device and uses Foundry Connect and Foundry Deploy.

## Windows Autopilot

A Microsoft cloud service that configures a new or reinstalled device for an organization when it first starts.

## Windows PE

Windows Preinstallation Environment, a minimal Windows that runs from memory. The target device starts into it from the deployment media. The Foundry OSD interface abbreviates it to WinPE.

## Windows PE startup

The console window that appears first when a device starts from the media. It prepares Windows PE and starts Foundry Connect, then Foundry Deploy.

## Windows RE

Windows Recovery Environment: the recovery tools stored in the file `winre.wim` of a Windows image. Foundry also builds the Windows PE boot image from it when the media needs Wi-Fi.

## Zero-touch

A method that needs no input from the technician during the deployment, because the administrator provided everything in Foundry OSD.
