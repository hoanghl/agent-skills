---
name: PR Implement
description: Implement a feature step from existing analysis, milestone, or prior-PR documents — restate the plan in a fixed template, save it as an MD file, then work a TODO checklist. Use when asked to implement a step that already has analysis or design docs.
---

# PR Implement

Three phases in order. No code in phase 1 or 2, and no skipping the restatement
to save time.

## Phase 1 — analyze the existing artifacts

Inputs come from the user: the analysis of the general problem, the
milestones/implementation plan, and optionally the related previous PRs.

- Nothing provided → stop and ask with the `question` tool for the doc paths or
  links (problem analysis, milestones, related PRs).
- A doc is missing or thin → ask for it before reading code.

Read in this order: analysis doc, milestone plan, previous PRs
(`gh pr view <n>`, `git log --oneline`), then the code they touch.

Also read the repo's own rules before touching code: `CLAUDE.md`, `AGENTS.md`,
`Makefile`, `.github/workflows/`, `README.md`. They define the commands, style,
and CI gates the implementation must honor; skip any that do not exist.

Record: the problem and its early signals, the milestone list, where this step
sits, the repo areas it lands in, and the tests that exist or are planned.

## Phase 2 — state the understanding

Print this template, filled in, as the reply body. Keep the headings verbatim.

```markdown
# 1. Why this general problem exists
- The problem in general, its early signals, the early analysis

# 2. How this PR can contribute to the overall plan
- Where this PR comes in the plan, which aspect of the problem it resolves,
  how it relates to previous and successive PRs

# 3. Implementation plan
- The step, and the part of the repo the implementation lands in

# 4. Testing strategy
- The existing test cases, or the strategy when there are none (e.g. external
  services, manual scenario)
```

- Short bullets only: one line, at most ~20 words, 1–3 per section.
- Ask for anything you cannot fill in; never invent it.

### Persist it

Write the same content to `<mmdd>-<content>.md` when the feature is important
or the text runs long. `mmdd` is today's date — zero-padded month and day, e.g.
`1030-cluster-membership.md` on 30 October. Put it in the directory of the
documents you analyzed; ask the user when that is unclear.

The file mirrors §1–§4 one-to-one: same headings, same bullets, nothing extra.

## Phase 3 — implement

Wait for the user's go-ahead ("go", "implement", "do it"). Then:

- No branch yet → use the `pr-branch` skill; never invent a branch name.
- MD file exists → append a `# TODO` section to it. Otherwise print the TODO
  list in chat.
- One checkbox per unit of work, ordered, sized to a single PR, one file or
  area each:

  ```markdown
  # TODO
  - [ ] Add `cluster.ts` with a Map keyed `ip:port`
  - [ ] Cover upsert, get, has, list, remove
  ```

- Tick items as they land: `- [ ]` → `- [x]`. Never mark an item done before its
  tests pass.
- Stay inside the plan; if reality forces a deviation, stop and say so.
- Run the §4 tests before reporting the PR ready.
