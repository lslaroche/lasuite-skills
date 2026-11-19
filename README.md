# lasuite-skills

Claude skills used and shared by the LaSuite team (DINUM), distributed as a plugin (`lasuite`). Anyone can install it, and every change pushed here reaches all synced accounts.

This repo is public. No personal content, no internal ids (PostHog projects, dashboards) and no real usage numbers here: everything written must be useful to the whole team and safe to publish.

## Tree

```
.claude-plugin/marketplace.json   Claude plugin catalog (must stay at the root)
plugins/                          the plugins, one folder per plugin
README.md
LICENSE
```

Anything added later (prompts, notes, evals, experiments) lives in its own folders at the root, next to `plugins/`. Claude only reads `.claude-plugin/marketplace.json` and the paths declared in it; the rest of the repo is invisible to it.

## The plugin catalog

`.claude-plugin/marketplace.json` is a *plugin marketplace*: a catalog that Claude accounts add once, and that serves them the plugins listed in it along with their updates.

| Plugin | Skills |
|---|---|
| `lasuite` | `obeya`, `ui-kit`, `qa`, `github-issue`, `product-framing`, `posthog-insight` |

The catalog name (`lasuite-skills`, defined in `marketplace.json`) is the suffix after `@` at install time.

### Requirements per skill

The skills check these accesses at startup and ask the user to connect them when missing.

| Skill | Required access |
|---|---|
| `github-issue` | `gh` logged in, local copy of the target repo, PostHog MCP |
| `qa` | Docs MCP (spec), Figma MCP (design), `gh` logged in (CI), local copy of the repo |
| `obeya` | PostHog MCP when the product sheet has a tracked stat |
| `ui-kit` | access to the `suitenumerique/ui-kit` repo: installed package, local clone or `gh` logged in |
| `product-framing` | Docs MCP or `gh` logged in when the material is a link |
| `posthog-insight` | PostHog MCP (read-only), access to the product's PostHog project |

### Product sheets (local)

`obeya` and `posthog-insight` read a product sheet that stays on each PM's computer, never in this repo: `~/.lasuite-skills/products/<product>.md`. It holds the PostHog project and dashboards, and for the obeya the tracked stat, its SQL query and the start of its history. The template is `plugins/lasuite/skills/obeya/references/product-sheet-template.md`.

Without a sheet, the skill asks the PM for the information, checks it in PostHog and offers to write the sheet. To share a sheet with another PM, send the file directly.

## Writing conventions

- Skills are written in English.
- Each skill has a "Language" section stating the language of its output: always English for anything posted on GitHub, French for Tchap posts, the user's choice for local documents.
- Facts that change with a dependency (components, tokens, versions) are read from their source repo, never hardcoded in a skill.
- Examples use fictional first names and numbers.

## Add this catalog to a Claude account

In claude.ai or the desktop app: **Customize > Plugins > Add > Add marketplace**, then enter `lslaroche/lasuite-skills`.

Then install `lasuite` from the list, and turn on **Sync automatically** on the catalog to receive updates (otherwise **Check for updates**).

From the command line (Claude Code):

```bash
claude plugin marketplace add lslaroche/lasuite-skills
claude plugin install lasuite@lasuite-skills
```

## Edit a skill

1. Edit the relevant `SKILL.md` under `plugins/<plugin>/skills/<skill>/`.
2. Bump `version` in `plugins/<plugin>/.claude-plugin/plugin.json`.
3. Open a pull request.
4. Once merged, accounts with automatic sync get the new version.

## Add a plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` with at least `name` (kebab-case) and `version`.
2. Add its skills under `plugins/<name>/skills/<skill>/SKILL.md`.
3. Add an entry to the `plugins` array of `marketplace.json`, with `name` and `source`.

Two rules that break the install if missed: a relative `source` must never contain `..`, and the `name` of a catalog entry must match the `name` of the corresponding `plugin.json`.

## Validate before pushing

```bash
claude plugin validate ./plugins/lasuite
claude plugin validate .
```

## License

MIT
