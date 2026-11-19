---
name: product-framing
description: "Frame a product idea before writing its spec: challenge the problem with six forcing questions, pick a scope mode, review it from five angles (user, design and accessibility, tech, strategy, operations), then hand the PM only the trade-offs that are theirs to make. Use when the PM brings an idea, a request, notes or a draft spec and wants to check it is the right problem (cadrage produit, cadrer un sujet, challenger une idée), or before writing a spec."
---

# Product framing

Inspired by gstack `/office-hours` and `/plan-ceo-review`.

Goal: avoid writing the spec of the wrong problem. Challenge first, review from several angles, then bring back to the PM only the trade-offs that are theirs to make.

Default context: LaSuite numérique products (DINUM), used by public agents. Open source, sovereignty, accessibility (RGAA), sobriety and interoperability matter. Adapt if the PM gives another context.

## Language

- Talk with the PM and write the synthesis in the PM's language (French by default).
- Anything later posted on GitHub is in English.

## Step 0: Gather the material

Start from what the PM provides (idea, notes, spec, ticket, user feedback). If nothing is provided, ask for a description in 3 to 5 sentences. Never invent user data or numbers.

If the material is a link (Docs, GitHub issue), check that the matching tool answers (Docs MCP, `gh auth status`). If it does not, tell the PM explicitly and ask them to connect it or paste the content.

## Step 1: Office hours, six forcing questions

Ask the questions one by one or all together (as the PM prefers), building on the answers. Be direct and kind, without complacency.

1. **Who exactly?** Which public agent profile, in which situation, how often? A real example?
2. **What pain, proven how?** What shows the problem exists (quotes, tickets, data, observation)? Or is it a hypothesis?
3. **What do they do today?** Current workaround, competing tool (including non-sovereign ones), and why it falls short.
4. **Why now, why us?** What makes this topic a priority for LaSuite rather than another one?
5. **The smallest useful thing?** The most reduced version that would bring real value and could ship fast.
6. **How will we know it works?** One or two measurable signals, and what would make us stop.

Flag explicitly: unverified hypotheses, a solution disguised as a problem, a fuzzy scope.

## Step 2: Scope decision

Present the 4 modes and recommend one, with its rationale:

- **Expand**: the real problem is bigger, there is a more ambitious opportunity.
- **Selectively expand**: add one or two high-leverage things.
- **Hold**: the scope is right, execute.
- **Reduce**: cut to ship faster or to de-risk.

## Step 3: Multi-role review

Review the framing from each angle. For each one: a score out of 10, what is missing for a 10/10, and 1 to 3 concrete recommendations.

- **User (public agent)**: real usefulness, simplicity, adoption, change management.
- **Design and accessibility**: journeys, consistency with the State design system (DSFR) and LaSuite, RGAA.
- **Tech**: feasibility, dependencies, interoperability with the other LaSuite building blocks, debt, security.
- **Strategy and DINUM**: alignment with the roadmap, sovereignty, pooling across administrations, communication and sponsors.
- **Operations and support**: support load, documentation, rollout to administrations.

## Step 4: Synthesis and trade-offs

Deliver a short synthesis:

1. The problem restated in one sentence (who / what / why).
2. Recommended scope (mode, content of the v1, what is out of scope).
3. Hypotheses to check and how (interview, data, prototype).
4. Main risks.
5. Trade-offs for the PM: only the taste or priority choices that only they can make, phrased as closed questions with a recommendation.
6. Next step: offer to continue with the spec-writing skill (write-spec) if available, passing it this synthesis.

If the PM wants, put the synthesis in a doc (Claude Docs) to share it.

## Tone

Concise and direct. Challenge the ideas, not the person. No needless jargon.
