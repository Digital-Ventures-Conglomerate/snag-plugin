# Changelog

## 1.0.1

- `snag-install` skill: describe where the public workspace key goes without naming credential-style environment variables (the key is public).

## 1.0.0

- Installs the Snag MCP server (`https://mcp.snag.ac/mcp`, OAuth sign-in) with its seven bug-report tools.
- Adds the `triage-snag-report` skill and `/snag:fix <id|url> [--pr]` to turn a Snag report into a fix branch and a drafted PR.
- Adds the `snag-install` skill for adding Snag's report-a-bug button to a web app.
