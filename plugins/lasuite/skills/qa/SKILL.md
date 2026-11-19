---
name: qa
description: "Produce a detailed functional QA document for a product release, by cross-checking the written spec, the design files and the actual code — one section per functional block, with test cases, scoped improvements and open decisions. Use this whenever the user mentions a QA, une recette, a test plan, un cahier de recette, reviewing a version before shipping, checking whether an app matches its spec or its Figma, or preparing a release readiness review — even if they don't say the word 'QA'. Also use it to review, extend or re-check an existing QA document."
---

# Functional QA of a product release

Produce a QA document that a team can act on: what works, what does not, what must be
built before shipping, and what still needs a decision. The value does not come from
listing features — it comes from *crossing three sources that usually disagree*: the
written spec, the design, and the code that actually shipped. Most real findings live in
those disagreements.

## The one rule that must never bend

**Never write an observation as if it had been verified when it has not.**

A QA document is trusted or it is worthless. When the analysis comes from reading code,
say so; the box stays unchecked. Only the human tester checks a box, and only for
something they exercised themselves in a running app.

This shapes every sentence. Write "expect X" or "should X", not "X happens". If a claim
comes from the code, it is a hypothesis about behaviour, not a result — the code says
what was intended, not what a user experiences. Being wrong here poisons the whole
document, because the reader cannot tell which lines to trust.

## Step 0 — Check access before anything else

A QA that quietly skipped a source because a connector was down is the worst outcome:
the document looks complete and is not. So check every tool the run will rely on
**before** the intake, with one cheap read call each, and report the result as a short
list (ok / missing) to the user.

- **Docs MCP** — call `docs_get_me`. Needed if the spec is a Docs URL.
- **Figma MCP** — call `whoami`. Needed for the design.
- **GitHub CLI** — `gh auth status`. Needed for issues, milestones and the CI state.
- **Local repository** — the path exists, `git remote get-url origin` points to the
  expected repo, and `git fetch` + `git status` show whether the copy is behind the
  remote. Stay read-only: never pull, checkout or reset on your own.
- **Notes vault** — the folder where the QA file will live exists and is writable.

For anything missing, **stop and ask the user explicitly**: connect it (say how), give
the information another way (a local copy of the spec, screenshots of the design, a path
to the repo), or knowingly go on without it. Never decide on your own to go without a
source. If they choose to go without it, write in the document which source is missing
and what that means for the findings.

Run the same check on the information itself once the intake answers come back: an
unknown branch or commit, a Figma URL that does not open, a spec page with no version
markers. Each of these is a question to the user, not a guess.

## Step 1 — Intake

Ask for these before starting. Do not guess them; a QA built on the wrong version or the
wrong spec is worse than no QA.

None of them is strictly required — a QA can be produced with two sources, or even one,
and refusing to start because something is missing helps nobody. But each source carries
a class of findings the others cannot produce, so what matters is knowing what you are
giving up. Ask for all of them, work with what you get, and **say in the document which
sources were actually crossed**. A reader who knows the design was never consulted reads
the result correctly; one who assumes it was does not.

1. **The spec** — a Docs URL (use the Docs MCP with the UUID from the URL, not a web
   fetch), a GitHub issue or milestone, or a local file. It defines the scope, so without
   it there is no way to tell a missing feature from an out-of-scope one, and the QA
   becomes a description of the product rather than a judgement on it.
2. **The design** — a Figma file URL (Figma MCP: `get_metadata` to locate frames by name,
   `get_screenshot` to actually look at them). Screenshots beat metadata for judging what
   a screen says. Without it you lose a whole class of findings — missing empty, error and
   loading states, wording that drifted, screens designed but never built — and none of
   them is visible from the spec or the code. If the user has no design to give, say
   plainly in the document that visual conformity was not assessed.
3. **The code** — a local repository path, plus the branch or commit under test. It is the
   only source that says what actually exists rather than what was intended; without it
   every statement about behaviour is a guess, and the document can only be a test plan to
   run, not a QA.
4. **The target version** — "v1", "beta"… Only lines marked for that version are in
   scope. Later-version lines matter only when something is already built ahead of scope,
   which is worth flagging.
5. **Language of the document** — anything posted on GitHub (issues, comments) is always
   in English. For the local document, ask: it can be French or English. Whichever is
   chosen, quote the real UI labels in their own language.
6. **Verification mode** — ask which one applies:
   - *code-derived only* — nothing is run; every box stays unchecked, waiting for a human.
   - *environment available* — some cases can be exercised. Then agree on how to mark
     them, because "verified by the assistant" and "verified by you" are different claims
     and the document must keep them apart.

Two optional inputs that repeatedly produce the best findings:

- **A previous or automated pre-QA** — use it as a lead, never as a verdict. Re-check
  each of its claims against the code before repeating it.
- **The CI state** — `gh run list` on the repo's workflows. Broken pipelines are invisible
  in the UI and nobody thinks to look. A translation pipeline that has failed silently for
  months is a real release blocker that no amount of clicking would reveal.

### Set up the working files

Write the QA into a **local `.md` file**, ideally inside the user's note vault (Obsidian
or similar). This is not a detail of storage: a markdown file on disk is the only setup
where the user and the assistant can write **at the same time** — they reword a finding
or tick a box in their editor while the next section is being drafted. A chat transcript
or a web document does not allow that.

The consequence is a working rule: **re-read the file immediately before every edit**.
The user's parallel changes are invisible otherwise, and overwriting them is the fastest
way to lose their trust in the whole exercise.

Ask the user where the file should live, and propose a folder holding the whole QA rather
than a lone file, so everything produced stays together.

**Copy the spec into its own `.md` next to it.** A spec fetched from an online document
cannot be grepped, quoted precisely, or re-read cheaply, and it silently changes under
you mid-QA. A local copy freezes the version being tested, makes every "the spec says X"
verifiable, and costs one fetch instead of many. Do the same for any pre-QA or design
notes worth keeping.

## Step 2 — Establish the section map, and get it validated

Before drafting anything, produce the **list of sections the QA will cover**, and have
the user confirm it. Sections are the skeleton of the document: discovering halfway
through that a whole functional block was missing, or that two sections overlap, means
rewriting what was already agreed. Ten minutes here saves hours later.

Build the list from all three sources, because each one knows something the others do
not:

- **The spec** gives the functional blocks and their scope — this is the backbone, and
  section titles should track it so the two documents can be read side by side.
- **The code** reveals what actually exists: screens and features that never made it into
  the spec, and spec blocks with no code at all. Both deserve a section.
- **The design** shows flows that exist visually but appear nowhere else.

Present the list with, for each section, one line saying what it covers, whether it is in
scope for the target version, and what state it appears to be in (built, partially built,
not built). Flag explicitly:

- blocks that are entirely unbuilt — they are features, not QA findings, and the user may
  want them out of the QA and tracked elsewhere;
- anything built but out of scope for this version;
- sections you are unsure how to split, and let the user arbitrate.

Then ask the user to confirm, reorder, merge or drop. Their answer is the plan for the
rest of the run.

## Step 3 — Offer a pre-QA sweep

Once the map is agreed, **ask** whether to run a broad automated pre-QA before the
detailed pass. Ask, do not assume: it reads across the whole codebase and **consumes a
significant amount of tokens**, so the user must choose knowingly. Say roughly what it
will cover so the choice is informed.

A pre-QA sweeps every section quickly and shallowly: for each one, is it wired to real
data or to fixtures, are there controls with no handler, permanently disabled elements,
`TODO`s, missing states? It also checks the CI. It produces leads, not verdicts.

Write it to **its own `.md` file** in the QA folder, separate from the QA document. Two
files, because they carry different levels of confidence: the pre-QA is fast and
unverified, the QA is deliberate. Merging them would let unverified claims inherit the
credibility of the verified ones.

Each finding in the pre-QA must then be re-checked against the code before it earns a
line in the QA document.

## Step 4 — Work one section at a time

Draft one functional block, show it, take corrections, then move to the next. This is
slower than producing everything at once, and it is what makes the document good: the
first corrections are almost always about method — tone, granularity, what counts as a
finding — and applying them retroactively to fifteen sections is painful and lossy.

Before editing the document, **re-read it**. The user very likely edits it in parallel in
their own editor, and overwriting their work destroys trust in the tool.

For each section, in order:

1. **Read the spec lines** for that block, and keep only the ones in scope for the target
   version.
2. **Read the code paths** that implement it. Look for what is wired versus what is only
   rendered: a button with no handler, a driver returning fixtures, a permanently disabled
   control, a `TODO`. These are the highest-value findings because the UI looks finished.
3. **Look at the design** for that block. Compare states, not just layout: empty states,
   error states, loading states, and the wording of labels. Missing states are the most
   common design gap.
4. **Check the tests** covering it. An end-to-end test asserting that a button "does not
   navigate" documents an inert control as expected behaviour — useful to know before
   someone wires it up.
5. Write the section using the template, then say plainly what you cross-checked and what
   you could not.

Look actively for **disagreements between sources**, and name them:
- the spec contradicting itself between two tables;
- something marked for a later version but already built;
- the design showing more than the spec asks for;
- a proposed cut that removes a line the spec marks as in-scope — always say "in the spec
  for this version, proposed to cut", never silently drop it, so the arbitration is
  visible.

## Step 5 — Output format

Read `references/output-template.md` for the exact skeleton, the status vocabulary and the
tags. Keep to it — a QA document is read by scanning, and a stable shape is what makes
scanning work.

Writing style, which matters more than it sounds:

- **Be concise.** One line per finding. The reader is deciding, not studying.
- **Describe behaviour, not implementation.** "The button does nothing" is useful to
  everyone; a function name is useful to no one but the author, and reads as blame. Keep
  file references for when the developer will genuinely need them.
- **Never point at the developer.** Findings are about the product. "Not wired yet" beats
  "the developer forgot".
- **Given/When/Then** when a case has a real precondition. Plain arrows (`→`) otherwise.
- **Quote real UI labels** exactly as they appear on screen, in their own language.

## Step 6 — Review passes

Once several sections exist, run these before the document is shared. Each one catches a
class of problem that makes a reader distrust the whole thing:

1. **Duplicates across sections.** The same click often appears in two blocks. Keep it
   where the assertion is specific and cross-reference from the other; a duplicate with
   two different checkbox states is worse than either.
2. **Contradictions between a case and its own finding.** An unchecked case whose bug
   line describes what happened when it was tried means it *was* tested — the box and the
   wording must agree.
3. **Status versus content.** A block marked fully functional while carrying a bug should
   say "partially functional".
4. **Scope labelling.** Every item sits under "implement now" or "later". If the split is
   the document's main message, it must be legible without reading the legend.
5. **Question count.** Questions are decisions, not work. Many of them signal an
   arbitration that has not happened. Reduce them by answering what the sources already
   answer, and keep only what genuinely needs a human call.

## Step 7 — Cross-cutting sweeps

Once the functional blocks are done, offer these. They are cheap, they find things no
feature-by-feature pass ever finds, and for a public-sector product some are obligations
rather than improvements:

- **Internationalisation** — compare the translation keys used in the code against the
  translated entries actually shipped. Gaps fall back silently to the source language.
  Check how translations are managed and whether that pipeline runs at all.
- **Accessibility** — cheap wins first: the `lang` attribute matching the real UI
  language, page titles that change with context, keyboard reachability of hidden or
  animated panels, focus handling in modals. A full audit is a separate exercise; say so
  rather than pretending a checklist covers it.
- **Reliability** — reconnection, session expiry, multiple tabs, repeated login cycles.
- **Performance and scale** — only worth a section when there is a realistic dataset to
  test against. On mock fixtures it produces noise; say that plainly instead of writing
  hollow checkboxes.

## What "done" looks like

The document lets someone who was not in the room answer three questions in under two
minutes: what works today, what is missing for this version, and what needs a decision
before anyone writes code. If a reader has to ask "but did you actually try it?", the
document has failed its first job — go back and make the verification status explicit.
