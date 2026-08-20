# Contributing to the documentation

Documentation changes are reviewed through pull requests in the GitBook repository.

## Before editing

- Verify behavior against the current Foundry source and supported release.
- Write task-based guidance for administrators and deployment technicians.
- Keep canonical paths stable because Foundry uses them for contextual help.
- Do not add credentials, tenant data, device identifiers, hardware hashes, or other sensitive information.

## Navigation and links

- Add every published page to `SUMMARY.md`.
- Use relative links between documentation pages.
- Update `.gitbook.yaml` redirects when an existing page moves.
- Run link and structure validation before opening a pull request.

## Images

Follow [IMAGE_CONVENTIONS.md](IMAGE_CONVENTIONS.md) for asset paths, filenames, accessibility text, placeholders, sensitive-data handling, and maintenance.

## Pull requests

Keep changes focused, explain why the documentation changed, and include the validation performed. Use an English Conventional Commit title.
