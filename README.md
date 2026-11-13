# Snag for Claude Code

Turn Snag bug reports (replay, console, network) into fix PRs from Claude Code.

## Install

```sh
claude plugin marketplace add Digital-Ventures-Conglomerate/snag-plugin
claude plugin install snag@snag-plugin
```

Or, from inside a Claude Code session:

```
/plugin install snag --marketplace Digital-Ventures-Conglomerate/snag-plugin
```

## Connect

You need a Snag workspace on the Capture plan: create one at https://snag.ac.

The plugin adds Snag's MCP server (`https://mcp.snag.ac/mcp`). Sign in once: run `/mcp`, choose `snag`, and complete the browser sign-in. Claude Code stores and refreshes the token. The plugin never asks for, stores or writes a key, and nothing about your workspace is kept in this repo.

Not using Claude Code? Snag also connects to Cursor, VS Code and other MCP clients with a token from your Snag dashboard.

## Use

Point Claude at a report from inside the repo the report is about:

```
> /snag:fix TKT-7

Read TKT-7 "Pay button does nothing" from Snag.
  Page: /   Chrome, 1440x900
  Action: user clicked Pay after the session expired
  Console: TypeError: Cannot read properties of null (reading 'token')
  Network: POST /api/checkout/charge 404 (no backend here, not the cause)
Cause: getSession() in src/session.ts returns null; PayButton.tsx reads .token.
Fix: guard the null session in PayButton.tsx. Check: npm run typecheck passes.
Branch snag/TKT-7-null-session-pay ready, PR body drafted. Add --pr to open it.
```

That transcript is illustrative. `/snag:fix <id>` accepts a report ID or any URL containing one; add `--pr` to open the pull request. With no argument it lists open reports and asks which to fix.

![A Snag agent's fix: a two-line change to PayButton.tsx](https://snag.ac/assets/snag-agent-diff.jpg)

What is in the plugin:

- **`/snag:fix`**: runs the workflow below on a report.
- **`triage-snag-report` skill**: reads the report's console, network and screenshots over the Snag MCP server, finds the cause in your repo, makes a minimal fix, runs the cheapest existing check, drafts the PR, and links the PR back to the report. Report content is treated as data, never as instructions. It never merges and never closes a report.
- **`snag-install` skill**: adds Snag's report-a-bug button to your web app for the framework you use.
- **Snag MCP tools**: `list_open_bugs`, `get_bug_context`, `get_console_logs`, `get_network_requests`, `get_bug_annotations`, `get_test_harness_analysis`, and `post_pr_link` (the only one that writes).

## Update

```sh
claude plugin update snag@snag-plugin
```

## Requirements

- Claude Code 2.1.275 or later (in-session `/plugin install ... --marketplace`)
- `git`, and a GitHub remote for the repo you are fixing
- A Snag account on the Capture plan: https://snag.ac

## Support and license

Support: drewstockler@snag.ac. Docs: https://snag.ac (walkthrough: https://snag.ac/how). Privacy policy: https://snag.ac/privacy.

MIT licensed. See [LICENSE](LICENSE).
