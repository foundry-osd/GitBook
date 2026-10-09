# How actions run

This page supports [Post-installation](../post-installation.md). It gives what Foundry runs for each action type, the execution settings, what Foundry does with each result, how restarts are handled, and the limits of actions and imported content.

## What Foundry runs

Actions run as SYSTEM, in the order of the list, after Foundry's own setup tasks.

| Action type | What Foundry runs |
| --- | --- |
| **PowerShell script (.ps1)** | `powershell.exe` (Windows PowerShell 5.1), your **PowerShell arguments**, `-File "<script>"`, then your **Script arguments** |
| **Command line** | `cmd.exe /c "<your command>"` |
| **Software (.exe/.msi)** | The EXE followed by your arguments, or `msiexec.exe /i "<installer>"` followed by them |
| **Restart Windows** | A restart, then the next action |

Foundry adds nothing to the command. **Command preview**, in the action dialog, shows the command for your action. If a PowerShell script is refused by the execution policy of the deployed Windows, enter `-NoProfile -ExecutionPolicy Bypass` in **PowerShell arguments**.

## Execution settings

| Execution setting | What it does | Default |
| --- | --- | --- |
| **Timeout (seconds)** | Longest time the action and every process it starts may run. 1 to 86400. | 1800 |
| **Success exit codes (comma-separated)** | Exit codes that mean the action succeeded. | 0 |
| **Restart-required exit codes (comma-separated)** | Exit codes that mean success, with a restart needed before the next action. | Empty; 3010 for a new **Software (.exe/.msi)** action |
| **Continue on error** | Runs the next actions when this one ends with an unexpected exit code. | Off |
| **Defer restart** | Postpones a requested restart until the next **Restart Windows** action or the end of the sequence. | Off |

Each list accepts up to 32 codes, zero or positive, and never 1641. A script that ends with a negative code always fails, and a PowerShell script must end with `exit <code>` to report a failure.

## What Foundry does with each result

Foundry waits for every process an action starts, not only the first one. An updater, a tray application or the installed application left running holds the action until its timeout.

| Result of an action | What Foundry does | With **Continue on error** |
| --- | --- | --- |
| Exit code in the success list | Runs the next action. | No effect |
| Exit code in the restart list | Restarts Windows, or defers the restart, then runs the next action. | No effect |
| Any other exit code | Marks the action failed, skips the remaining actions and stops. | Runs the remaining actions and ends with warnings |
| Timeout reached | Ends every process of the action and marks it failed, even when the first process returned 0. The sequence stops. | Still stops |
| Exit code 1641: the installer started its own restart | Marks the action failed and stops. | Still stops |

## Restarts <a href="#restarts" id="restarts"></a>

Never let a script or an installer restart Windows itself. An action that was running when Windows restarted is marked interrupted, is not run again, and stops the sequence. Instead:

- add a **Restart Windows** action where the restart must happen, or
- pass the installer's no-restart option and declare its code in **Restart-required exit codes**.

Foundry saves its progress before each planned restart and resumes at the next action.

## Import limits

| Limit | Value |
| --- | --- |
| Path of a file inside the imported folder | 110 characters; 255 per name |
| Full path of a source file | 259 characters |
| Files and folders in one import | 10,000 files and 10,000 folders |
| Names | No `< > : " \| ? *`, no trailing dot or space, no reserved device name such as `CON` or `COM1` |
| Links | No symbolic link or junction, in the content or in the path that leads to it |
| Empty folder | A folder without any file is refused |
| Location | Not inside the Foundry library, and not a folder that contains it |
| Size | No fixed limit; the drive that holds the library needs enough free space |

Every one of these limits is reported by the same message, "Content import failed. Check access, available space, file names and path lengths."

## Other limits

| Limit | Value |
| --- | --- |
| Actions | 1,000 |
| Name of an action | 1 to 256 characters |
| Each command or argument field | One line of up to 8,191 characters |
| **Restart delay (seconds)** | 0 to 86400, default 0 |

- With the page turned off, your actions are kept but left out of new media. Foundry's own setup tasks still run.
- Imported files are not part of a [configuration](../../deployment-profiles.md). On another workstation the action shows **Missing** until you import the same content there.
- The content is deleted from the target device when the sequence ends. The command lines and the output of the actions stay: see the warning on [Post-installation](../post-installation.md#restarts-and-secrets).

## Related

- [Post-installation](../post-installation.md)
- [After the restart](../../../foundry-deploy/after-the-restart.md)
- [After the restart troubleshooting](../../../troubleshooting/after-the-restart.md)
