---
name: obeya
description: "Generate the weekly OBEYA post of a LaSuite product, to paste in Tchap, including the weekly stat fetched from PostHog with all-time highs. One post = one product. Use when the PM asks for the obeya, the weekly or the hebdo of a LaSuite product."
---

# Weekly obeya

Generate the weekly post a PM pastes in Tchap for their product. A post covers one product only: if the PM handles several, write one post per product.

## Language

- The post is in French: it is read in Tchap by French-speaking teams. The format and the example below are the output, keep them in French.
- Talk with the PM in their language.

## Product sheets

Each product has its sheet on the PM's computer, never in this repo: `~/.lasuite-skills/products/<product>.md`. It holds internal ids and numbers that must not be published: exact post title, PostHog project and dashboards, tracked stat, SQL query, start of the history, line format and known pitfalls. The `posthog-insight` skill reads the same sheet.

List the folder, and read the product's sheet first. Without a sheet (or without the folder), see "Product without a sheet" below.

## At startup

If the sheet has a tracked stat, check that the PostHog MCP answers and that the sheet's project is accessible (a simple read call, switch to that project if needed). If PostHog is not connected or the project is not accessible, tell the PM explicitly and ask them either to connect PostHog or to give the number. Never output the post with the stat silently removed.

## Tracked stat and ATH

When the sheet has a tracked stat, the stats section is never removed, even if the PM gives no number.

1. Period: the last complete week (Monday to Sunday) before today.
2. Run the sheet's SQL query (`execute-sql`) on the sheet's project. Do not read an insight's result: it can ignore a filter (see the sheet's pitfalls).
3. The query returns every week since the start of the history. Count nothing before that date.
4. Delta: compute the difference with the previous week and write it with its sign (e.g. `+12`, `-27`, `+0`). On the first week of the history there is no previous week: leave the delta out.
5. ATH: if the last week's value is strictly greater than every previous week, add `🏆 ATH`. A tie is not a record.
6. Write the line in the sheet's format, always with the delta, and with `🏆 ATH` only on a record.
7. If the last week is missing, is 0 or drops sharply from the previous one, tell the PM before writing the line: it is often a broken filter or a missing tag, not a real drop.

Outside the post block, give in one line the previous week's value and the previous record, so the PM can check.

## Product without a sheet

1. Ask the PM for the exact product name and whether they track a stat every week.
2. No tracked stat: write the post without a sheet, the stats section stays optional.
3. A tracked stat: ask for the PostHog project and the reference dashboard or insight, then the exact definition (event, filters, unit counted). Write the SQL query, run it and show the weekly series to the PM for validation. Find the first reliable week (e.g. when a tag first appeared): it is the start of the history.
4. Write the sheet from `references/product-sheet-template.md` to `~/.lasuite-skills/products/<product>.md`, after the PM approves it. If the file system is not reachable, deliver it as a file and tell the PM where to save it: until it is saved, the stat's definition only exists in this conversation and will be lost.
5. Never add a product sheet to the skills repo, and never write a real number or id in a skill: the repo is public.

## Format (always this one)

```markdown
### [Produit - OBEYA]

**Semaine dernière :**

* ...

**Travaux en cours :**

* ...

**Les stats :**

* ...

**L'info à faire passer :**

* ...

**Les présent·es cette semaine :**

* ...
```

The title is **always in square brackets, around both the product name AND the word OBEYA**: `### [Docs - OBEYA]`. Never output the title without brackets, or with brackets around the product name only.

Section titles are **always bold** (`**Travaux en cours :**`), for readability in Tchap. Inclusive writing in "Les présent·es".

Keep the blank lines between sections (otherwise bullets render badly once pasted in Tchap).

Output a single markdown code block, ready to paste in Tchap.

## Sections

**Semaine dernière**: what was shipped or finished since the last obeya, up to the day of the post. A few lines, only visible deliveries (release, production launch, app plugged in). Leave out technical details (JWT claims, scopes, scripts' internals). Optional section if nothing was completed.

**Travaux en cours**: about 4 lines, factual, focused on progress rather than completeness. Group the details of one topic on a single line. Status emojis (▶️ in progress, ⏸️ paused) only when the PM gives them, never added by default. People's names stay only where the PM put them, as an aside on a topic (e.g. "unreads (Alex)").

**Les stats**: optional section, unless the product sheet has a tracked stat. If there are no solid numbers this week, remove the section entirely. Never fill it with N/A, a guess or a joke.

**L'info à faire passer**: optional section. It merges the info to share and any request (no separate "Demandes" section). It can carry a call to action through Tchap reactions (e.g. "Réagissez avec ☎️ et on vous recontacte"): offer one when the PM's notes contain a request to the readers. If nothing is really essential this week, remove the section rather than padding it.

**Les présent·es cette semaine**: first names only. Never add the Tchap room name after the names, or any presence detail (days on site, remote work, etc.). Mention absences in parentheses.

## How to proceed

1. Identify the product (ask if ambiguous) and read its sheet if it exists. If there is a tracked stat, fetch it as described above.
2. Take the PM's notes as they are and sort them into the right sections. No needless rewording, but condense: meeting notes are much longer than the post.
3. Leave out the meeting's open questions (trade-offs, "to discuss"): they stay in the meeting and do not go in the post.
4. Remove empty optional sections rather than filling them.
5. Output a single markdown block.

## Reference example

MonCompte & Console post (fictional first names and numbers):

```markdown
### [MonCompte & Console - OBEYA]

**Semaine dernière :**

* Release 0.5.0 et script de release
* Passés à MonCompte : 💙 Transcript, Grist, Assistant, Hub 💙
* MFA LaSuite : organisation avec le support, webinaire ProConnect MFA le 26/11

**Travaux en cours :**

* Déploiement v0 : Tchap, Visio, Menshen
* Création du user à l'introspection (débloque les users Menshen, permet les interops sans s'être préalablement connecté au produit) (Camille)
* MFA LaSuite : dev des issues (Camille)
* Fluidifier la connexion aux apps de LaSuite : fin de conception cette semaine (Sacha)
* Articulation Menshen x MonCompte x Users : doc technique pour le choix de la solution (Alex)

**Les stats :**

* 1234 WAU (+56 vs la semaine précédente) 🏆 ATH

**Les présent·es cette semaine :**

* Camille, Sacha, Alex
```
