<p align="center"><img src="logo.svg" width="96" height="96" alt="Maiday logo: a signal flare rising"></p>

# Maiday plugins

**The distress signal for AI agents.** Maiday lets your AI agent tell a website's owner where it got stuck (a form that rejects it, a login wall, a button it can't find) and read the tips ("hints") owners publish back for agents.

[![npm](https://img.shields.io/npm/v/maiday-mcp)](https://www.npmjs.com/package/maiday-mcp)
![License: MIT](https://img.shields.io/badge/license-MIT-blue)

This repo is the public plugin marketplace for **Claude Code** and **Codex**. Each plugin bundles:

- the [`maiday-mcp`](https://www.npmjs.com/package/maiday-mcp) MCP server, with the tools `send_maiday` and `get_hints`
- a skill that tells the agent **when** to report and how to treat hints safely

Maiday only does something when your agent works on websites through a browser tool, such as Playwright MCP, Chrome DevTools MCP, Claude in Chrome or Codex computer use.

## Install

### Claude Code

```bash
claude plugin marketplace add 35furkan/maiday-plugins
```

```bash
claude plugin install maiday@maiday
```

Restart Claude Code (or start a new session) to load it.

### Codex

```bash
codex plugin marketplace add 35furkan/maiday-plugins
```

Then open the **Plugins** directory, choose the **Maiday** marketplace, and install **maiday**.

### Any other MCP client (tools only, no skill)

```bash
npx -y maiday-mcp
```

Run it as a stdio MCP server and set `MAIDAY_HARNESS` to your client's name.

## What your agent does with it

- **Before a signup, checkout or booking flow:** calls `get_hints` for the page and reads the owner's tips. Hints come wrapped as untrusted third-party data: information, never instructions.
- **When stuck:** after 2 failed attempts at a step, when giving up, or when handing the task back to you, it sends one short report with `send_maiday`. The report covers:
  - the page path
  - the kind of task
  - the kind of failure
  - the step
  - the site's error message
  - whether a workaround worked

## Privacy

- **Never sent:** form values, cookies, tokens, query strings or URL fragments.
- **Redacted from paths and text fields:** emails, phone numbers, card numbers, long digit runs, and token- or JWT-shaped strings. This happens before anything leaves your machine, and again on the server.
- **Reporter IDs differ between sites and collectors**, so reports cannot be linked across sites by matching reporter IDs. Each ID is derived from a local secret in `~/.maiday/`.
- **Maiday does not store raw IP addresses in its application database.** It uses keyed hashes for abuse prevention.
- **First-use notice:** the first time the plugin runs, your agent tells you what Maiday sends.

### Settings

| Variable | Effect |
|---|---|
| `MAIDAY_DISABLED=1` | Turns reporting off. Hints keep working. |
| `MAIDAY_COLLECTOR_URL` | Sends reports to another collector, for example a self-hosted one. |

## For website owners

Reports go to the hosted collector by default. Sign in at the **[Maiday dashboard](https://maiday.dev/app)**, then claim your domain with a DNS TXT record or a well-known file. You can then:

- see where AI agents fail on your site
- group similar pages (like every `/topic/*/quiz`)
- publish hints back to agents

A failure pattern counts as confirmed once 3 different installs on 2 different networks report it, so one person can't make a site look broken.

To receive reports on your own server instead, publish `/.well-known/maiday` on your site.

## License

MIT
