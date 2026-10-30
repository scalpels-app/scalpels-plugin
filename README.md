# Scalpels for Claude

One connection to your [Scalpels](https://scalpels.app) apps, plus skills that
teach Claude how to run them well.

Scalpels is a catalog of free, MIT-licensed Laravel apps that you deploy to
your own Laravel Cloud or Forge account. This plugin connects Claude to the
Scalpels MCP server, which can read the catalog and docs, plan and run
deployments, connect existing sites, and relay questions to the apps your team
has installed.

## Install

```shell
claude plugin marketplace add artisan-build/scalpels-plugin
claude plugin install scalpels@scalpels
```

On first use, Claude opens a browser so you can sign in to Scalpels and approve
access. There are no tokens to copy.

For headless or CLI setups, create a token on the **Agent access** page in
Scalpels instead. See the [agent docs](https://scalpels.app/docs/scalpels/agents).

## What's included

| Component | What it does |
| --- | --- |
| `scalpels` MCP server | `https://scalpels.app/mcp`, authenticated with OAuth |
| `scalpels-fleet` skill | Orients Claude in your teams and installed apps, and sets the ground rules for deploying, connecting sites and calling apps |

## What Claude can do with it

The OAuth connection grants the `mcp:use` scope. With it, Claude can:

- read your teams, sites, deployments, the catalog and docs
- connect a Forge site or Cloud environment to a product you run, which writes
  that product's keys into the site's environment
- plan and start deployments to your Laravel Cloud account
- call your installed apps' tools as you, with your role in each team

Each team still enforces your current role. Revoking the connection on the
**Agent access** page stops the current token and its ability to refresh.

## Unattended jobs

A scheduled job that only collects telemetry or investigates problems doesn't
need to deploy, connect sites or change anything in your apps. MCP clients hold
one connection per server, so the connection itself can't be narrowed per job.
Use your client's per-tool permissions instead:

- Allow the read tools (`app_read`, `installed_apps`, `app_tools`,
  `list_deployments`, `deployment_status`, `deployment_drift`, `docs`) without
  asking.
- Set `app_write`, `app_call`, `deploy_app`, `connect_site`, `resume_deployment`,
  `isolate_app` and `restore_app` to **ask**.
- Set `app_confirm` (destructive app actions) to **ask** or **never**.
- Run scheduled tasks without automatic approval. A job that reaches a write
  then stops at the approval prompt instead of acting on its own.

This works because Scalpels routes every app call through one of three doors
(`app_read`, `app_write`, `app_confirm`) according to what the tool does, so
the client can gate each door separately.

## Where your data goes

Your apps and their data live in your own Laravel Cloud or Forge account.
Scalpels holds no product data.

When Claude calls one of your installed apps, the request and response pass
through Scalpels' memory on the way. They are not end-to-end encrypted.
Scalpels does not intentionally store that content; it records who asked which
app for what, when, and whether it succeeded.

If you'd rather not route through Scalpels at all, point your client directly
at your apps' own MCP endpoints. Fork button is up and to the right.

## License

MIT
