# After the restart

After Foundry Deploy restarts the target device, Windows Setup finishes the installation and the **Foundry Post-installation** console runs the last tasks before the first sign-in. This page tells you how to read it and when the device is ready.

<figure>
  <img src="../.gitbook/assets/shared-post-installation-01-console-progress.png" alt="Foundry Post-installation console over the Windows setup screen, running action 4 of 5 with three succeeded actions, one running and Cleanup waiting">
  <figcaption>The console lists Foundry's tasks and the administrator's actions in the order they run.</figcaption>
</figure>

## What to do

1. Keep the device on power and on the network, and let Windows Setup run.
2. Watch the console when it opens. Do not turn the device off: a task that was running when the device stopped is not run again.
3. Let the device restart by itself. Before a planned restart the console shows `Restarting in <n> seconds...` or `Restarting now...`, then `Waiting for a Windows Setup restart.` Afterwards it reopens with `Resuming after restart` and continues with the next task.
4. Read the last line before the console closes.

| Last line | What it means |
| --- | --- |
| `Post-installation completed.` | Every task succeeded. `Continuing Windows Setup in <n> seconds...` counts down from 10, then the console closes. |
| `Post-installation completed with warnings. Review the execution result and logs.` | Windows Setup continues after the same countdown, but a task failed or left something behind. Check before you hand over the device. |
| `Post-installation stopped. Review the execution result and logs before continuing Windows Setup.` | A task failed and the remaining ones were skipped. The console closes at once, without a countdown. |

No console opens when the deployment has nothing to run after the restart. The console is always in English.

## Read the console

| Line | Meaning |
| --- | --- |
| `Preparing Windows before OOBE` | First run. After a planned restart it reads `Resuming after restart`. |
| `Action 4 of 5` | The task in progress. The total counts Foundry's tasks, the administrator's actions and `Cleanup`. |
| `- [Running] Google Chrome 00:01` | One line per task: status, name, elapsed minutes and seconds. A failed line adds `(exit <code>)` when the task returned one. |
| `Log: C:\Windows\Temp\Foundry\Logs\PreOobe\Foundry.PostInstall.log` | The log of the console itself. Script and installer output is in other files. |

A task goes from `[Waiting]` to `[Running]`, then `[Succeeded]` or `[Failed]`. `[Skipped]` means it never started because the sequence stopped before it. `[Interrupted]` means it was running when Windows restarted or lost power.

<details>
<summary>Foundry's tasks, in the order they run</summary>

Only the tasks the deployment needs are listed.

| Name in the console | Listed when |
| --- | --- |
| `Install drivers` | The driver pack is a Lenovo or Surface installer that runs in Windows. |
| `Configure network` | The media copies its Wi-Fi or Ethernet 802.1X profile to Windows. |
| `Join domain and place computer`, `Verify domain membership` | Domain Join is configured. The second runs after the restart that follows the join. |
| `Remove AI components` | Copilot or AI Hub removal is selected. |
| `Remove AppX packages` | AppX removals are selected. |
| `Activate Windows` | See [Windows activation](#windows-activation). |
| The administrator's actions | Always after Foundry's tasks, under the names the administrator gave them. |
| `Cleanup` | Always last. |

</details>

## Domain Join lines

With Domain Join, two more lines report the join:

```text
Domain - Join: Succeeded; Placement: Succeeded; Membership: Succeeded
Restart: Completed; Cleanup: Disposed
```

| Part | Meaning |
| --- | --- |
| `Join` | The device joined the domain. A failed join does not stop the other tasks. |
| `Placement` | The computer account is in the requested OU. `Skipped` when no OU was requested. |
| `Membership` | Checked after the restart: the device belongs to the expected domain under the expected name. |
| `Restart` | The restart that follows the join: `Required`, `Requested`, then `Completed`. |
| `Cleanup` | `Disposed` once the temporary copy of the join credentials is deleted, `Pending` until then. |

A yellow line below them names what to follow up, for example `Domain joined; target OU placement failed`. For any value other than `Succeeded`, or `Cleanup: Pending` at the end, see [Domain Join troubleshooting](../troubleshooting/domain-join.md).

## Windows activation

`Activate Windows` is listed only when Windows came from the catalog with a Retail license and **Use Foundry settings** was the answer file. It is never listed for a custom Windows image, a Volume license or a custom answer file.

`[Succeeded]` means the task finished, not that Windows is activated. Foundry activates Windows Home and Pro editions with the OEM key held in the firmware, when that key is for the installed edition; otherwise it changes nothing. Activation needs Internet access. Check **Settings > System > Activation** in Windows.

## Windows Autopilot window

With the interactive hardware hash upload, the Autopilot window opens later, on the first Windows setup screens, after this console has closed. See [Windows Autopilot step](autopilot.md).

## Hand over the device

The device is ready when:

- the console ended with `Post-installation completed.`, or you have checked every warning;
- with Domain Join, the checks of the [Domain Join step](domain-join.md) pass;
- Windows shows its sign-in screen or its first setup screen.

After the console has closed, its results are in `C:\Windows\Temp\Foundry\State\PreOobe\execution-result.json`.

## If the console stops or shows a failure

[After the restart troubleshooting](../troubleshooting/after-the-restart.md) starts from the line you see and tells you which file to open.
