---
name: ui-kit
description: "Build React interfaces for LaSuite apps (Docs, Drive, People, Messages, etc.) with the LaSuite design system, and apply LaSuite brand colors, typography and tokens. Every component, prop, import and token is read from the suitenumerique/ui-kit repo for the version in use, never from memory. Use when the user works on a LaSuite app, mentions @gouvfr-lasuite/ui-components, @gouvfr-lasuite/ui-tokens, the former @gouvfr-lasuite/ui-kit, @gouvfr-lasuite/cunningham-react or @openfun/cunningham-react, Cunningham, DSFR, or the LaSuite brand (charte graphique)."
---

# LaSuite design system

## Principles

- The repo `suitenumerique/ui-kit` is the only source of truth. It is the canonical home of the LaSuite design system and contains both the former UI Kit and Cunningham.
- Never write a component name, prop, import path, token name or value from memory, and never copy one from this skill: they change between versions. Read them in the repo or in the installed package, for the version the project uses.
- If something cannot be found in the repo, say so. Do not invent a component or a prop.
- In every answer, say which version and which files the code is based on, so the user can check.

## Language

- This skill and its answers to the user: English, unless the user writes in another language.
- Generated code follows the project's conventions. Interface labels are in the product's language (usually French).

## At startup

Check these before writing code. If one is missing, tell the user explicitly and ask what to do. Do not go on from memory.

1. **Version in use**: read the target project's `package.json` and the installed version in `node_modules/@gouvfr-lasuite/`.
2. **Former packages**: if the project uses `@gouvfr-lasuite/ui-kit`, `@gouvfr-lasuite/cunningham-react`, `@gouvfr-lasuite/cunningham-tokens` or `@openfun/cunningham-react`, tell the user they are replaced, and point to `docs/MIGRATION.md` and its codemod. Run the codemod only in preview mode; apply it (`--write`) only after the user approves.
3. **Access to the source**, in this order:
   - the installed package (`node_modules/@gouvfr-lasuite/ui-components/dist/*.d.ts` for types and exports);
   - a local clone of `suitenumerique/ui-kit`, at the tag that matches the installed version;
   - the repo online with `gh` (`gh api repos/suitenumerique/ui-kit/contents/<path>?ref=<tag>`), after checking `gh auth status`.
   If none of these is available, ask the user for a local clone or for `gh` access.
4. **New project**: no version installed yet. Read the latest release and `src/docs/installation.mdx`, and say which version the code targets.

## Where to look in the repo

| Need | Where |
|---|---|
| Packages and what replaces what | `README.md`, `docs/MIGRATION.md` |
| Install, provider, first steps | `packages/ui-components/src/docs/installation.mdx`, `first-steps.mdx` |
| Themes, light and dark modes, customization | `packages/ui-components/src/docs/theming.mdx`, `customization.mdx` |
| Public exports (what can be imported) | `packages/ui-components/src/index.ts`, `package.json` (`exports`) |
| A component: API, props, usage | `packages/ui-components/src/components/<component>/` (source, `*.mdx`, `*.stories.tsx`) |
| Real usage examples | the component's stories and `packages/ui-components/e2e/` |
| Colors, spacings, typography | `packages/ui-components/src/docs/colors.mdx`, `spacings.mdx`, `typo.mdx` |
| Exact token names and values | `packages/ui-components/src/cunningham-tokens.css` (generated), engine in `packages/ui-tokens/` |
| Icons | `packages/ui-components/src/icons.ts`, `src/components/icon/` |
| Breaking changes between versions | `packages/ui-components/CHANGELOG.md`, `packages/ui-tokens/CHANGELOG.md` |
| Rendered docs, to check visually | Storybook: https://suitenumerique.github.io/ui-kit/ |

If a path no longer exists, list the repo tree to find the new one rather than guessing.

## Brand fundamentals

These are principles. All values come from the repo.

- **Source order**: the design system repo first. DSFR (`github.com/GouvernementFR/dsfr`) is the government baseline, used only for what the design system does not cover.
- **Tokens, never raw values**: use the semantic (contextual) tokens for colors, spacings and typography, never a hardcoded hex or pixel value. Pick the token by role (background, content, border; brand, neutral, success, error...), as described in `colors.mdx`.
- **Typography**: the fonts shipped by the package (Marianne for LaSuite). Import them through the paths in `package.json` `exports`.
- **Themes**: light and dark modes come from the design system themes. Do not write separate colors for dark mode.
- **Accessibility**: keep the contrast and focus behaviour of the components. Do not override them with custom styles.

## How to work

1. Before building something custom, look for an existing component in `src/components/` and in Storybook.
2. Read the component's source and stories before using it: required props, accepted values, the provider it needs.
3. Check that it is exported from the package root (or from a public subpath) in the installed version.
4. Use the provider and setup described in `installation.mdx` for the installed version.
5. After writing the code, list the files and the version it relies on.
