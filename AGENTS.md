# AGENTS.md

## Project and scope

Grace's landing page is a small, statically prerendered SvelteKit site using Svelte 5, TypeScript and Tailwind. It introduces the Pomodoro timer and app/website blocker; the desktop application's native functionality lives in a separate repository.

Read `README.md` for setup, deployment and the upstream licensing caveat. Use `package.json` and `pnpm-lock.yaml` as the command/dependency authorities. Keep this guide focused on enduring project decisions, not session history or temporary server state.

## Working loop

1. Read the affected source and its callers before editing. State a short plan for multi-file or behavioral changes; use existing components, styles and dependencies first.
2. Implement the smallest complete change. Match neighboring Svelte/TypeScript conventions; keep browser-only code in `onMount` or event handlers so prerendering remains safe. Avoid unrelated cleanup, speculative abstractions and repository-wide formatting.
3. Search all affected callers and duplicate patterns after changing a contract. Update the implementation, consumers and relevant documentation together; remove code made obsolete by the change.
4. Verify the changed behavior using the checks below, then inspect the final diff for scope, accidental generated output and secrets.
5. Make a small checkpoint commit when the change is coherent and verified. Report what changed, checks actually run and any remaining limitation. Never describe an unrun check as passing.

Ask before adding dependencies, changing deployment behavior or expanding product scope. Preserve pre-existing user changes; do not discard or quietly include them in your work.

## Source map

| Path | Responsibility |
| --- | --- |
| `src/routes/+page.svelte` | Landing identity, animation scheduler, tagline, project/developer rows and their local styles |
| `src/routes/+layout.svelte` | Global CSS import, centered page shell and responsive outer spacing |
| `src/routes/+layout.ts` | Static prerendering contract |
| `src/app.css` | Shared palette, typography and global styles |
| `src/lib/assets/` | Bundled assets, including JetBrains Mono and its license |
| `svelte.config.js` | Static adapter and `BASE_PATH` handling |
| `.github/workflows/ci.yml` | Reusable Svelte/TypeScript and root/prefixed static build checks |
| `.github/workflows/pages.yml` | GitHub Pages deployment, gated by reusable CI |
| `src/routes/privacy/+page.svelte`, `+page.ts` | Standalone privacy policy and directory-style static URL |

Inherited routes/components are not a mandate to redesign or remove them. Inspect them only when the task affects them.

## Product and UI invariants

- Preserve the navy/red palette, locally bundled JetBrains Mono, terminal-character identity, compact composition and generous whitespace. Reuse CSS tokens rather than hardcoding new colors.
- Keep the logo and `grace.` wordmark side by side down to 320px. Keep wordmark cells stationary; animate the existing hard-edged color wave, not the text geometry.
- Default logo mode is ripple. Native button activation cycles **ripple → ASCII donut → blocky flip → ripple**. Each mode persists until activation; no timed switching or smooth 3D/extruded replacement.
- Preserve a static identity without JavaScript and under initial or live reduced-motion preferences. Keep one animation scheduler, suspend work when the document is hidden and remove listeners/cancel frames on unmount. Do not claim hidden-tab behavior verified unless the browser actually reports the page hidden.
- Keep copy minimal and preserve the current tagline unless the task changes it. Avoid adding sections, controls or a new component framework without a request.
- Repository, releases, documentation, developer GitHub and LinkedIn are real, same-tab links. Portfolio remains visibly `pending` and noninteractive until a real destination is supplied; never invent URLs.
- Keep matching SVG icons left of each row label. Reuse installed `@lucide/svelte`; decorative icons use `aria-hidden="true"`. Preserve readable labels, right-aligned status/chevrons, native keyboard activation and visible focus styles.
- Retain `@sveltejs/adapter-static`, prerendering and both root/custom-domain and repository-subpath builds. Use SvelteKit's base-path utilities for new internal or public-asset URLs. Do not introduce server-only endpoints or runtime secrets into the static page.

## Commands and verification

Use Node.js >=20.18.1 and pnpm 10. With dependencies already installed, run from the repository root:

```sh
pnpm run dev
pnpm run check
pnpm run build
BASE_PATH=/grace-landing-page pnpm run build
```

- `pnpm run check` is the full Svelte/TypeScript gate. Run it after source changes; run the production build after rendering, asset, route or configuration changes. Exercise both build variants when base-path behavior changes.
- There is currently no `test` or `lint` script. Do not invent commands or install tooling just to claim coverage. If tests are introduced, run the entire affected test file/module, not only a selected passing test, and keep assertions tied to observable behavior.
- For UI changes, open the actual page and inspect it at 320px and a desktop width. Check wrapping/overflow, icon alignment, keyboard focus, link destinations and pending rows. For animation changes, also exercise all modes, reduced motion and no-JavaScript output; check browser errors.
- For build/path changes, preview the generated output at the actual prefix. A development-page screenshot does not prove the static deployment works.
- For documentation-only changes, validate paths, commands and claims against the repository; application rebuilds are unnecessary unless executable examples/configuration changed.
- Keep pre-existing warnings distinct from regressions. The original red ASCII art has previously triggered a contrast warning; report actual audit results rather than hiding a finding or claiming a universally clean audit.

## Commit early and often

- Prefer small, cohesive local commits at verified milestones instead of one large end-of-task commit. Each commit should be understandable and reversible on its own; avoid broken checkpoints or artificial one-line churn.
- Use concise Conventional Commit messages, such as `feat: add link row icons`, `fix: preserve mobile identity layout` or `docs: clarify agent workflow`. Explain non-obvious tradeoffs in the body when needed.
- Before staging, inspect `git status` and the diff. Stage explicit paths or your own hunks; inspect the staged diff and file names before committing. Leave unrelated/pre-existing changes untouched, especially when they share a file.
- Honor explicit no-commit requests. Do not amend existing commits, rewrite history, change branches destructively, push, open a PR or deploy without explicit permission. A local commit does not authorize publishing; pushing `main` can trigger Pages deployment.
- Keep credentials and private data out of code, logs, screenshots and commits. Confirm secret files are ignored before committing; never force-add them. Keep `node_modules/`, `build/` and `.svelte-kit/` untracked. Update the lockfile only for an intentional dependency change.

## Maintaining these instructions

Update this guide when project contracts change. Keep `README.md` human-facing; do not duplicate this file into tool-specific instruction files. Use a nested guide only when a subtree genuinely needs different rules.

Structure and principles informed by the [AGENTS.md specification](https://agents.md/), [Ruff's repository guide](https://github.com/astral-sh/ruff/blob/main/AGENTS.md) and [Apache Airflow's repository guide](https://github.com/apache/airflow/blob/main/AGENTS.md): concrete commands, explicit boundaries, scoped changes and evidence-backed verification. Their project-specific tooling and policies do not apply here.
