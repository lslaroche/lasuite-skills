# Output template

## Document skeleton

The header is folded so the document opens on content, and it survives being pasted into
a GitHub issue or a wiki. Keep the blank lines after `</summary>` and before `</details>`,
otherwise the markdown inside is not rendered.

```markdown
<details>
<summary><b>About this QA</b></summary>

Run on DD/MM/YYYY, against `<branch>` @ `<commit>` (date), with <environment>.
Specs: <url>
Design: <url>

**How to read this document**
- **Status**: how the block behaves as a whole.
- **Test cases** — ✅ tested and working, ◻ either not verified yet or explicitly noted as not functional.
- **Implement for <version>** — to do before shipping. **Future** — potential improvements, only if everything for <version> is done.
- Tags: (bug) broken, (ux) / (ui) design fix, (enhancement) new behaviour, (question) needs a decision.

</details>

# <Functional block>

## <Flow inside that block>
**Status:** <status>

**Test cases:**
- [x] <case that was exercised and works>
- [ ] <case still to run, or run and found broken — say which>

**Implement for <version>:**
- [ ] (tag) <finding>

**Future:**
- [ ] (tag) <finding>
```

`Test cases` describe observable behaviour a human can reproduce. `Implement for
<version>` and `Future` carry findings and open decisions. A flow with nothing to fix
simply has no such block — do not pad.

## Status vocabulary

| Status | Meaning |
|---|---|
| `Functional 👍` | Works as intended, nothing blocking |
| `Needs corrections ⚠️` | Works but with defects, or partially |
| `Not functional ❌` | Present in the UI but does nothing, or broken |
| `Not implemented yet ⏸️` | Not built at all, or mockup / fixtures only |

A block carrying a bug is not `Functional 👍`. Readers calibrate on these four words, so
using them loosely is what makes a QA document stop being read.

## Tags

| Tag | Use for |
|---|---|
| `(bug)` | Broken behaviour |
| `(ux)` | Interaction or flow to fix |
| `(ui)` | Visual or wording fix |
| `(enhancement)` | New behaviour, not a defect |
| `(question)` | Needs a human decision — not work, and not assignable |
| `(spec)` | The spec and what exists disagree |
| `(note)` | Context worth recording, no action |

Keep `(question)` scarce. Every question is an unmade decision; the useful move is to
answer the ones the spec, design or code already answer, and surface only what genuinely
requires a call.

## Worked example

From a real QA of a chat application. Note the mix: verified cases checked, unverified
ones left open, real UI labels quoted in the product's language, findings split by scope,
and a bug written from the user's point of view rather than the code's.

```markdown
## New chat entry point
**Status:** Functional 👍

**Test cases:**
- [x] Click "Nouveau" → search picker opens
- [x] Select a user with an existing DM → existing DM reused, no duplicate created
- [x] Type a non existing user → "Aucune personne trouvée" error message

**Implement for v1:**
- [ ] (ux) Click "Nouveau" → auto focus on "Rechercher des personnes"
- [ ] (bug) Given I select two members of an existing group → When the chat opens → Then a DM should open by default, not the group

**Future:**
- [ ] (ux) Add ⌘+K shortcut to open "Nouveau"
```

And a section for something that exists on screen but does nothing — the most common and
most valuable finding, because the UI looks finished:

```markdown
## Start a meeting
**Status:** Not functional ❌

**Test cases:**
- [ ] Click "Démarrer une réunion" → expect a new meeting to open in a new tab - not functional

**Implement for v1:**
- [ ] (question) In the spec's Navigation table for v1, but the Meetings table marks every
      meeting feature as later — the two disagree, this needs an explicit call.
```
