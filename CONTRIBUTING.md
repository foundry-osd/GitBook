# Contributing to the documentation

Documentation changes are reviewed through pull requests in the GitBook repository.

## Before editing

- Verify behavior against the Foundry `main` branch and document it as released.
- Write task-based guidance for administrators and deployment technicians.
- Keep canonical paths stable because Foundry uses them for contextual help.
- Do not add credentials, tenant data, device identifiers, hardware hashes, or other sensitive information.

## Writing rules

The documentation is a usage guide. A reader must be able to act on a page without reading the others.

- **One home per fact.** Explain a behavior on the page that owns it. Other pages carry one sentence and a link. Do not append a section about a new feature to unrelated pages.
- **Open with the screen.** A page that describes a screen starts with one or two sentences saying what the screen is for, then its screenshot, then the steps.
- **Use the labels of the app.** Text in bold is the exact English label shown in Foundry. Menu paths use the section and item names of the app.
- **State numbers.** Write defaults, limits, sizes and timeouts as the app enforces them.
- **Keep internals out.** Do not describe classes, pipelines, development setups or anything the reader cannot see or act on.
- **Use few hints.** At most two hint blocks on a page, never two in a row. Reserve `danger` for data loss.
- **Collapse reference material.** Put tables that most readers skip in a `<details>` block.
- **Keep task pages short.** Aim for 300 to 700 words. Split a longer page or move reference material out.

Page structure:

| Page type | Structure |
| --- | --- |
| Foundry OSD feature | Purpose, screenshot, **Before you start**, **Configure**, options table, **What the technician sees**, **Check the result**, **Limits**, **Related** |
| Foundry Connect or Foundry Deploy step | Purpose, screenshot, steps, meaning of each field or status, what can stop you, next step |
| Section landing page | Two sentences, then a table "I want to ... / Go to" |
| Troubleshooting | Symptom index, then one section per symptom titled with the exact on-screen message, each with **Where**, **Cause**, **Fix** and **Collect** |
| Reference | Facts in tables, no procedures |

Terminology:

| Use | Instead of |
| --- | --- |
| Windows PE | WinPE, except when quoting a label |
| Post-installation | PostInstall, except in a path or file name |
| technician, administrator | operator |
| target device | the computer, except for Active Directory terms such as computer account |
| deployment media | boot media |
| USB drive | USB media, USB key |
| Deployment password | technician password |
| custom Windows image, then custom image | custom WIM |
| configuration | profile, for Settings backup and sync |
| computer name | machine name |
| answer file | unattend file |
| join account | domain account |
| restart | reboot |

## Navigation and links

- Add every published page to `SUMMARY.md`.
- Use relative links between documentation pages.
- Update `.gitbook.yaml` redirects when an existing page moves.
- Run link and structure validation before opening a pull request.

## Images

Follow [IMAGE_CONVENTIONS.md](IMAGE_CONVENTIONS.md) for asset paths, filenames, accessibility text, placeholders, sensitive-data handling, and maintenance.

## Pull requests

Keep changes focused, explain why the documentation changed, and include the validation performed. Use an English Conventional Commit title.
