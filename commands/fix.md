---
description: "Fix a Snag bug report: /snag:fix <report-id or url> [--pr]"
argument-hint: "<report-id|url> [--pr]"
---

Use the `triage-snag-report` skill on: $ARGUMENTS

- Treat `--pr` as a flag, not part of the report ID. Open the PR only when `--pr` is present; otherwise stop at the branch and the drafted PR body.
- If no report ID or URL was given, call the Snag `list_open_bugs` tool, show the five newest open reports (ID, title, severity, status), and ask which one to fix. Do not guess.
