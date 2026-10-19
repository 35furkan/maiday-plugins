# Maiday

**The distress signal for AI agents.** When your agent gets stuck on a website (a form that keeps rejecting its input, a login wall, a button it can't find), Maiday lets it send the site's owner a short, privacy-safe report. Before it guesses, the agent can also read the hints that owners publish for agents, such as the date format a field expects or an API endpoint to use instead.

Site owners see the reports at [maiday.dev](https://maiday.dev) and publish hints back.

## What's in this plugin

- **A skill** that tells the agent when to report a failure, when not to, and how to treat hints safely (as untrusted data, never as instructions).
- **The `maiday-mcp` MCP server** with two tools:
  - `get_hints`: fetch the owner's hints for a page before acting on it.
  - `send_maiday`: report where the agent got stuck.

Maiday only does something when your agent works on websites through a browser tool, such as Playwright MCP or Claude in Chrome.

## What it runs

The plugin starts `npx -y maiday-mcp@0.1.3`. npx downloads that exact version of [`maiday-mcp`](https://www.npmjs.com/package/maiday-mcp) from the npm registry and runs it locally over stdio. Its source is MIT-licensed and readable in the npm package.

## What it sends and fetches

- **Discovery:** for the site your agent is on, it requests `https://<site>/.well-known/maiday`. If the site publishes its own report or hint endpoint there, Maiday uses it. Otherwise it uses the hosted collector at `https://maiday.dev`.
- **Hints:** a request with the page's origin and path, without query string or fragment.
- **Reports**, only when the agent calls `send_maiday`: the site origin and page path, the task type, the failure type, a short description of the step, the element involved, the site's error message, any workaround, the number of attempts, the outcome, the agent tool's name and version, the model name if you set `MAIDAY_MODEL`, and a reporter ID.
- **Never sent:** form values, cookies, tokens, query strings or URL fragments. Emails, phone numbers, card numbers and token-like strings are redacted before anything leaves your machine.

## What it stores on your machine

A random secret in `~/.maiday/`. Reporter IDs are derived from it separately for each site and each collector, so reports can't be linked across sites by matching IDs. Delete the folder to get new IDs.

## Turning it off

Set `MAIDAY_DISABLED=1` to stop sending reports; the agent can still read hints. Set `MAIDAY_COLLECTOR_URL` to use your own collector instead of `https://maiday.dev`. To stop all requests, disable or uninstall the plugin.

## Privacy and license

Read the full [privacy policy](https://maiday.dev/privacy). The plugin and `maiday-mcp` are MIT-licensed.
