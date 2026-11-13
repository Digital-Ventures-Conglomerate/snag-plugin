---
name: snag-install
description: Use when the user wants to add Snag's report-a-bug button to their web app or asks how to set up Snag.
---

# Add Snag's report-a-bug button to a web app

Snag is one script tag. Visitors click a button, and Snag captures the session replay, console and network with personal data masked in the browser, then files a ticket. This skill adds that tag the way the project already does things.

Public docs: https://snag.ac/how (the three ways to show the button) and https://snag.ac/try. Do not invent options beyond what they list.

## Before editing

1. The user needs a **workspace key** (it looks like `wk_live_…`), created in the Snag dashboard at https://snag.ac during onboarding, where they also verify the domain the app runs on. If they have no workspace yet, send them to https://snag.ac and stop.
2. **Do not ask the user to paste the key into chat, and never write its value into a file.** Choose the environment variable name, wire the code to read it, and tell the user to put the value in their own untracked env file (for example `.env.local`) or deploy settings themselves.
3. The key is meant to be seen by browsers: Snag only accepts it from the user's verified domains. An env var keeps it out of version control; it does not make it secret in the page.

## The tag

```html
<script src="https://snag.ac/embed.js"
  data-snag-key="<workspace-key>"
  defer></script>
```

Optional `data-snag-mode`:

- omitted: a floating "Report" pill
- `launcher`: the launcher style shown at https://snag.ac/how
- `api`: no button; the app supplies its own "Report a bug" link or button

Pick the default unless the user asks for another style.

## Detect the framework and place the tag

Look at the repo (`package.json`, `Gemfile`, `manage.py`, `wp-config.php`, `index.html`) and use the matching pattern. Read the key from an env var the project already uses for public config.

| Framework | Where | Key |
|---|---|---|
| Next.js | `app/layout.tsx` (or `pages/_app.tsx`) using `next/script` with `strategy="afterInteractive"` | `process.env.NEXT_PUBLIC_SNAG_KEY` |
| Vite / React | `index.html` `<head>`, `data-snag-key="%VITE_SNAG_KEY%"` | `VITE_SNAG_KEY` in `.env.local` (gitignored) |
| Plain HTML | the shared `<head>` or layout partial | inject at deploy time; if the site has no build step, leave a clearly marked placeholder (`<workspace-key>`) and tell the user to fill it in at deploy, since the key will be in the page source either way |
| Rails | `app/views/layouts/application.html.erb` | `ENV["SNAG_KEY"]` or credentials |
| Django | the base template, key passed from settings | `SNAG_KEY` setting from the environment |
| WordPress | `functions.php`: enqueue `https://snag.ac/embed.js` with `wp_enqueue_script`, then add `data-snag-key` with a `script_loader_tag` filter (`wp_enqueue_script` cannot set custom attributes) | a constant defined in `wp-config.php` |

Add the variable name (never its value) to `.env.example` or the project's equivalent, and make sure the real `.env*` file is gitignored. If the project has a Content-Security-Policy, the script and the Snag ingest host must be allowed; say so rather than guessing the host.

## Confirm it works

1. Deploy (or open a preview) on a domain the user has verified in the Snag dashboard. Snag only records on verified domains; whether and how a local dev server can be authorized is a dashboard question, so point the user there rather than assuming `localhost` works.
2. The Snag button should appear. Click it, describe a fake bug, submit.
3. The ticket shows up in the Snag dashboard, and in the connected tracker (Jira or GitHub Issues) if one is set up.

If the button is missing: open the page source or the Elements panel and check the script tag is present with its `data-snag-key` attribute filled in (frameworks that inject scripts can drop or empty attributes, and an env var that was not inlined at build time leaves it blank), check the browser console for a Snag message, and confirm the domain is verified in the dashboard.

## Out of scope

Tracker setup, domain verification and billing happen in the Snag dashboard; link to https://snag.ac rather than reimplementing them. For fixing a report once it arrives, use `/snag:fix`.
