---
name: github-issue
description: "Write a GitHub issue for a suitenumerique repo in a short format: Problem to solve, Acceptance criteria (Gherkin), Event tracking, Out of scope, Implementation. Use when the PM asks to write an issue, a ticket or a GitHub task (une issue, un ticket)."
---

# LaSuite GitHub issue

## Language

- The issue is always in English, like everything posted on GitHub.
- Talk with the PM in their language.

## Principles

- Short and classic. Do not use the repo's Q&A template (`Feature_request.md`: "Is your feature request related to a problem...").
- One issue = one problem. No alternatives, no migration strategy, no reminder of the repo's conventions (tests, changelog: already in CONTRIBUTING).
- Never guess a technical fact: check it in the code of the local repo (up to date with the remote), the repo's docs or external docs (e.g. ProConnect partner docs), and tell the PM what is not verified.
- If something is unclear (scope, target user, expected behaviour, events to track), ask the PM before writing, all questions at once. Do not guess.
- Git is read-only: never change the repo (no commit, checkout, reset or automatic pull).
- No emojis, no em dashes.

## At startup

Check access first. If something is missing, tell the PM explicitly and ask what to do. Never go on silently with less information.

- `gh auth status`: `gh` is logged in to GitHub.
- PostHog MCP: it answers (a simple read call). Otherwise, ask the PM to connect it or to give the existing event names. Each product has its own PostHog project (Compte, Docs, Visio, Fichiers, Hub...): switch to the target product's project (`projects-get`, `switch-project`), and switch back after looking at another one.
- Target repo: the request names it clearly. Otherwise, ask which one.
- Local copy: the working folder is that repo (`git remote get-url origin` points to `suitenumerique/<repo>`). Otherwise, ask for the path to the local copy.

## Before writing

1. Run `git fetch` and `git status` to compare the local copy with the remote. If there are commits to pull, or if the branch is not `main`, tell the PM and wait for their answer before reading the code.
2. Read the relevant code to name the right area (files, signals, settings) and spot what already exists (e.g. a hardcoded claim, a logger already configured).
3. If the issue spans several apps, check the dependencies between repos (e.g. which ones use django-lasuite, in which version).
4. Look for a close existing issue (`gh issue list --search`) and suggest linking it rather than duplicating. Open online (`gh issue view`) any issue the PM links to, or that the request obviously refers to.
5. Find the existing tracking: `posthog.capture` / `capture(` / `posthog.identify` calls in the code, events already in the product's PostHog project (PostHog MCP, read-only), and the project settings (`project-get`: autocapture, session replay). Apply the event tracking rules below.
6. Use the product's agreed wording for every UI term, in the issue and in event names: the translation file, and any open wording PR (it wins even before it is merged). Never invent a label.
7. If nothing seems worth tracking (refactoring, internal bug), ask the PM rather than deciding.
8. For a bug, gather the steps to reproduce and the clues before writing (see below).

## Bugs

A bug issue is only useful if the developer can reproduce it or knows where to look. Steps to reproduce and clues matter as much as the acceptance criteria.

- **Everything goes in Problem to solve.** No separate Steps to reproduce section: it would repeat the problem. Problem to solve describes the bug, then gives the steps and clues when we have them, and links the screenshots or videos.
- **Steps to reproduce.** Write them from what the PM and the users who hit the bug report: numbered steps, the observed result, and what differs between users (e.g. "a reload fixes it for one user, not for the other"). Only when they add something to the description. When there are no reliable steps yet, say so in one sentence.
- **Look for clues in every tool we have.** Existing PostHog events (error reasons, environment, first and last time seen, number of users), the code path that shows the error message (translation key, then the component and the condition), recent commits in that area (`git log`), and related issues. Ask the PM for console logs or screenshots when they exist.
- **Put the clues in Problem to solve**, not in Implementation: the developer may not read Implementation. Keep in Implementation only the leads in the code.
- **Privacy.** No names, emails or person ids from PostHog in the issue: write "one user in production".
- **Event tracking is the exception.** PostHog tracks usage first, not every bug. Do not add events or properties only to understand a bug by default: weigh the pros and cons, and propose one only when the existing data cannot answer and the event will stay useful after the fix. Ask the PM before adding it.

## Event tracking rules

These rules apply to every event an issue adds, so that all LaSuite products track the same way. Accounts (`suitenumerique/accounts`, `core/utils/analytics.py`) is the reference implementation.

- **Naming.** Events are `domain:action`, with the action in kebab-case: `auth:log-in`, `account:created`, `message:sent`. Properties are in snake_case. Reuse an existing event before creating one.
- **Identity.** The distinct id is the app's user id (`str(user.pk)`), the same in the frontend and the backend. Never the email: it changes, and products that mix email and id see one person split in two. The email and the ProConnect/MonCompte `sub` go in person properties (`$set`). Flag it in Implementation if the code identifies by email today.
- **No content.** No message text, title, file name, prompt or technical id (room id, Matrix id). Every event carries `environment` and `release`, added by the single sending helper, never by each call.
- **Few events.** Track only what answers the questions in Problem to solve. Prefer one event with an `action` property (and a `trigger` or `source` property for the gesture: button, right click, shortcut...) over one event per button: it ranks actions against each other in a single chart. Do not track a feature that does not exist yet: name it in Out of scope.
- **Bounded values.** Properties take a short list of values or buckets (`2-5`, `6-20`, `20+`), never raw counts, free text or ids.
- **Volume.** An event that can fire many times per screen (per message, per item) fires once per object, or becomes a daily summary event with counts and a ratio.
- **Front or back.** Say where the event can be observed. Data the backend never sees (end-to-end encrypted content, pure UI gestures) can only be tracked in the frontend. When the choice is open, give it as a lead in Implementation, do not decide it.
- **Existing standards.** When an open standard already measures the same thing (e.g. matrix-analytics-events for Matrix decryption failures), reuse its thresholds and properties so the numbers can be compared, and keep the LaSuite naming.
- **Privacy settings.** If session replay or autocapture is on in the PostHog project and the screens show sensitive content, say so in Implementation.

## Format

- Title: short, descriptive, with an area prefix when useful (`ProConnect: ...`, `Backend: ...`).
- Only use labels that exist in the repo.

```markdown
## Problem to solve

2 to 4 sentences, from the user's or the team's point of view: what is wrong today and why it matters. Start with "In order to..." when possible. Say if it is measurement only (nothing is blocked).

For a bug, the same section then gives the steps to reproduce and the clues when we have them (see Bugs), with links to screenshots or videos. No separate section.

## Acceptance criteria

(gherkin block)
Given ...
When ...
Then ...
And ...

3 to 4 scenarios max: nominal case, empty or missing case, update case, failure case.

## Event tracking

- `domain:action`: when it fires (`property_1`: `value_a`, `value_b`; `property_2`)

PostHog events to create or change, following the event tracking rules. Group them by journey when there are more than 5. List person properties separately. No priority column: list only what ships with this issue. Leave the section out when the issue adds no event, which is the usual case for a bug.

## Out of scope

Only when there is a real limit (e.g. apps not using django-lasuite: Tchap, Grist, Projects). Link to a follow-up issue.

## Implementation

TBD by default. Otherwise 1 to 3 factual leads (e.g. "OIDC back-channel logout in django-lasuite, a first draft exists").
```

## Delivery in the chat

- The title alone, in its own code block.
- The body in a second code block, fenced with 4 backticks so it can contain the `gherkin` block.
- List in 1 to 3 bullets what still needs checking (e.g. access to a ProConnect claim, the security officer's expectations, the assignee's GitHub handle).

## Example

```
ProConnect: Request the roles scope and track public agent roles
```

````markdown
## Problem to solve

In order to know precisely what type of public agents use our apps we need to track the ProConnect claim `roles`: `agent_public`, `agent_public_etat`, `agent_public_territorial` (array, can be combined, may be empty or absent).

Measurement only: nothing is filtered or blocked.

## Acceptance criteria

```gherkin
Given a user signs in with ProConnect
When ProConnect returns the `roles` claim
Then Accounts stores it with the identity provider data
And the user's PostHog person properties include `roles`

Given ProConnect returns an empty `roles` array
Then `roles` is stored as an empty array in PostHog

Given an existing user signs in again with ProConnect
Then their `roles` are updated
```

## Event tracking

- `identity_provider:data`: user signs in with ProConnect (`backend_roles`)

Person property: `roles`, updated at each sign-in.

## Implementation

TBD
````
