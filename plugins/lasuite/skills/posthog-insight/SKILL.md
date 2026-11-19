---
name: posthog-insight
description: "Prepare the PostHog AI prompts to create, change or check an insight or a dashboard for a LaSuite product, test the queries on real data first and check the result after creation. Use when the PM asks to create, fix, change or check a PostHog insight, chart, SQL/HogQL query or dashboard (un insight, un graphique, un dashboard)."
---

# PostHog insight

Claude creates nothing in PostHog: the connector is used read-only. Claude checks the data, writes the prompt the PM pastes into PostHog AI, then checks what was actually created.

## Language

- PostHog AI prompts, insight titles and descriptions: in French, like the dashboards they land in.
- Talk with the PM in their language.

## Product sheets

Each product has its sheet on the PM's computer, never in this repo: `~/.lasuite-skills/products/<product>.md`, shared with the `obeya` skill. It holds the PostHog project, the dashboards (one per environment) and known pitfalls. The repo is public: never write a project id, dashboard id or real number in a skill.

Read the product's sheet first. Without a sheet, ask for the project and the dashboards, check them in PostHog, then offer to write the sheet (PostHog section, plus known pitfalls) at that path, after the PM approves it.

Events and properties are not listed in the sheet: read them from PostHog every time, since they change.

## At startup

Check that the PostHog MCP answers and that the sheet's project is reachable (a simple read call, switch project if needed). Otherwise, tell the PM explicitly and stop: no prompt without a test on real data first.

## How dashboards are organised

- Environment and test accounts: filtered only at the dashboard level, never in the insight.
- Period: set on the dashboard. The insight can default to 30 days, but no hardcoded date in the SQL and no period in the title.
- One insight, added to every dashboard concerned (production, preproduction, staging). Never one copy per dashboard.

## Method (mandatory steps)

1. Check in the PostHog schema that the events and properties exist.
2. Test the query in SQL on real data, filtered as the dashboard would (environment, test accounts), and write down the expected values.
3. Give the prompt to paste in a code block, then the expected values so the PM can check.
4. After creation: read the insight's definition again, run it in the context of each dashboard concerned, compare with the expected values.
5. Look for duplicates (insights with the same name) and report any gap between what PostHog AI announces and what actually exists.
6. One insight at a time. Wait for the PM's approval before moving to the next one.

## Writing PostHog AI prompts

- Always write "crée UN SEUL insight et ajoute-le aux dashboards X, Y, Z". Without it, PostHog AI creates one copy per dashboard.
- Always write "n'ajoute AUCUN filtre d'environnement ni de comptes de test". Without it, it adds them or moves them.
- Give the exact event and properties, and forbid it to suggest others.
- SQL: give the full query, with "utilise exactement cette requête, ne la modifie pas".
- Fix: list only what changes, and end with "ne touche à rien d'autre".
- Types supported by PostHog AI's creation tool: Trends, Funnel, Retention, SQL. For the others (Lifecycle, etc.), guide a manual creation in the interface step by step.
- Title and description in French, without a period.

## SQL rules (HogQL)

- Always `AND {filters}` in the `WHERE`: it applies the dashboard's period and environment.
- A SQL insight run alone, outside a dashboard, does not apply the environment filter: always check it in the dashboard's context.
- The chart's X axis must point to a column present in the `SELECT`.
- Handle `NULL` in joins and conditions (`ifNull(x, '')`).
- Ratio in Trends: formula `B/A` with the axis in `percentage_scaled`, not `B/A*100`.
