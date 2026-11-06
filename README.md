# Scalpels for Claude and Codex

One connection to your [Scalpels](https://scalpels.app) apps, plus skills that
teach your agent how to run them well.

Scalpels is a catalog of free, MIT-licensed Laravel apps that you deploy to
your own Laravel Cloud or Forge account. This plugin connects your agent to the
Scalpels MCP server, which can read the catalog and docs, plan and run
deployments, connect existing sites, and relay questions to the apps your team
has installed.

## Install

### Claude Code

```shell
claude plugin marketplace add artisan-build/scalpels-plugin
claude plugin install scalpels@scalpels
```

### Codex

With a recent Codex CLI that supports `codex plugin`:

```shell
codex plugin marketplace add artisan-build/scalpels-plugin
codex plugin add scalpels@scalpels
```

For a local checkout, use `codex plugin marketplace add /absolute/path/to/scalpels-plugin`
instead of the GitHub source. Both clients use the same `skills/` directory
and `.mcp.json` endpoint; installing in one client does not install in the other.
Do not add the MCP server separately as well.

Authorize the Scalpels OAuth connection when prompted by your client so you
can sign in and approve access. There are no tokens to copy.

For headless or CLI setups, create a token on the **Agent access** page in
Scalpels instead. See the [agent docs](https://scalpels.app/docs/scalpels/agents).

## What's included

| Component | What it does |
| --- | --- |
| `scalpels` MCP server | `https://scalpels.app/mcp`, authenticated with OAuth |
| `scalpels-fleet` skill | Orients your agent in your teams and installed apps, and sets the ground rules for deploying, connecting sites and calling apps |
| `hone-investigate` skill | Checks Hone coverage, errors, slow routes and abnormal telemetry per app; proposes fixes without making changes |
| Claude and Codex manifests and marketplaces | Install the same plugin from this repository in either client |

## What your agent can do with it

The OAuth connection grants the `mcp:use` scope. With it, your agent can:

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

When your agent calls one of your installed apps, the request and response pass
through Scalpels' memory on the way. They are not end-to-end encrypted.
Scalpels does not intentionally store that content; it records who asked which
app for what, when, and whether it succeeded.

If you'd rather not route through Scalpels at all, point your client directly
at your apps' own MCP endpoints. Fork button is up and to the right.

[Privacy](https://scalpels.app/privacy) · [Terms](https://scalpels.app/terms) —
both policies, published October 30, 2026, are live on scalpels.app and are
configured in both manifests.

## Package references

The Codex compatibility manifest follows OpenAI's official
[package guide](https://developers.openai.com/plugins/build/plugins) and
[manifest field reference](https://developers.openai.com/plugins/deploy/submission#automatically-provide-submission-and-review-information).
Its `skills` and `mcpServers` paths are relative to the plugin root. The native
Codex marketplace at `.agents/plugins/marketplace.json` uses the documented
[marketplace format](https://developers.openai.com/plugins/build/plugins#marketplace-metadata)
and coexists with `.claude-plugin/marketplace.json` without moving the Claude
plugin. The shared `.mcp.json` declares the remote HTTP URL; OAuth endpoints,
dynamic client registration and the `mcp:use` scope are discovered from the
server's OAuth metadata. No client secrets or bearer tokens are packaged.

The listing icon is the existing Scalpels mark from `public/favicon.svg` in
[`artisan-build/scalpels.app`](https://github.com/artisan-build/scalpels.app/blob/66ac00547525bae9dc69d3cb4df19bf87e0aeb07/public/favicon.svg),
rasterized without redrawing to a 1024 × 1024 PNG with ImageMagick:

```shell
magick -background none -density 192 ../scalpels.app/public/favicon.svg -resize 1024x1024 -strip .claude-plugin/icon.png
```

Review the icon before the plugin is first saved or submitted in Claude's
developer portal: that first save sets the listing icon, and later changes to
the file don't update it. A successful local validation does not replace the
directory's safety scan or policy review. Claude's directory can validate and
review a private repository, but the repository must be public before the
listing goes live; that visibility change and all directory submissions remain
human steps.

## License

MIT
