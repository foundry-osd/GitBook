# After the restart

After Foundry Deploy restarts the target device, Windows Setup finishes the installation and the **Foundry Post-installation** console runs the last work before the first sign-in. This page tells you how to read it and when the device is ready.

<figure>
  <img src="../.gitbook/assets/shared-post-installation-01-console-progress.png" alt="Foundry Post-installation console over the Windows setup screen, running action 4 of 5 with three succeeded lines, one running and Cleanup waiting">
  <figcaption>The console lists Foundry's tasks, then the administrator's actions, in the order they run.</figcaption>
</figure>

## What to do

1. Keep the device on power and on the network, and let Windows Setup run.
2. Watch the console when it opens. Do not turn the device off: whatever was running is not run again.
3. Let the device restart by itself. The console announces a planned restart and reopens after it with `Resuming after restart`.
4. Read the status line, the coloured line under the list, before the console closes.

| Status line | What it means |
| --- | --- |
| `Post-installation completed.` | Everything succeeded. `Continuing Windows Setup in <n> seconds...` counts down from 10, then the console closes. |
| `Post-installation completed with warnings. Review the execution result and logs.` | Windows Setup continues after the same countdown, but something failed or was left behind. Check before you hand over the device. |
| `Post-installation stopped. Review the execution result and logs before continuing Windows Setup.` | A line failed and the remaining ones were skipped. The window closes at once, without a countdown: read the result in `execution-result.json`, as [After the restart troubleshooting](../troubleshooting/after-the-restart.md) explains. |

No console opens when the deployment has nothing to run after the restart. The console is always in English.

## Read the console

The list holds Foundry's own tasks, then the administrator's actions, then `Cleanup`.

| Line | Meaning |
| --- | --- |
| `Preparing Windows before OOBE` | First run. OOBE is the series of Windows first-run screens. |
| `Action 4 of 5` | The line in progress. The total counts Foundry's tasks, the administrator's actions and `Cleanup`. |
| `- [Running] Google Chrome 00:01` | Status, name, elapsed minutes and seconds. A failed line adds `(exit <code>)` when the program returned one. |
| `Restarting in <n> seconds...` or `Restarting now...`, then `Waiting for a Windows Setup restart.` | A planned restart. Progress is saved; let the device restart. |
| `Log: C:\Windows\Temp\Foundry\Logs\PreOobe\Foundry.PostInstall.log` | The log of the console itself. Script and installer output is in other files. |

A line goes from `[Waiting]` to `[Running]`, then `[Succeeded]` or `[Failed]`. `[Skipped]` means it never started because the sequence stopped before it. `[Interrupted]` means it was running when Windows restarted or lost power.

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
| `Cleanup` | Always, after the administrator's actions. |

</details>

## Domain Join lines

With Domain Join, two more lines report the join. When they read as follows, the join is complete:

```text
Domain - Join: Succeeded; Placement: Succeeded; Membership: Succeeded
Restart: Completed; Cleanup: Disposed
```

`Placement: Skipped` is normal when no OU was requested. A yellow line below them names what to follow up. For any other value at the end, see [Domain Join troubleshooting](../troubleshooting/domain-join.md).

<details>
<summary>What each part means</summary>

| Part | Meaning |
| --- | --- |
| `Join` | The device joined the domain. A failed join does not stop the rest of the sequence. |
| `Placement` | The computer account is in the requested OU. `Skipped` when no OU was requested. |
| `Membership` | Checked after the restart: the device belongs to the expected domain under the expected name. |
| `Restart` | The restart that follows the join: `Required`, `Requested`, then `Completed`. |
| `Cleanup` | `Disposed` once the temporary copy of the join credentials is deleted, `Pending` until then. |

</details>

## Windows activation

`Activate Windows` is listed only when Windows came from the catalog with a Retail license and **Use Foundry settings** was the answer file. It is never listed for a custom Windows image, a Volume license or a custom answer file.

`[Succeeded]` means the task finished, not that Windows is activated. Foundry activates Windows Home and Pro editions with the OEM key held in the firmware, when that key is for the installed edition; otherwise it changes nothing. Activation needs Internet access. Check **Settings > System > Activation** in Windows.

## Windows Autopilot window

With the interactive hardware hash upload, the Autopilot window opens later, on the first Windows setup screens, after this console has closed. See [Windows Autopilot step](autopilot.md).

## Hand over the device

The device is ready when:

- the status line was `Post-installation completed.`, or you have checked every warning;
- with Domain Join, the checks of the [Domain Join step](domain-join.md) pass;
- Windows shows its sign-in screen or its first setup screen.

## If the console stops or shows a failure

[After the restart troubleshooting](../troubleshooting/after-the-restart.md) starts from what you saw and tells you which file to read.
