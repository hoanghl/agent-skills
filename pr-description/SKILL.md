---
name: PR Description
description: Compose a short, bullet-point pull request description (Why / Idea / Changes / Test suite) from the actual code and diff. Use when asked to write, rewrite, or update a PR description.
---

# PR Description

Every line must earn its place. Short, succinct, bulleted, no filler. A reviewer
should understand the PR in under 30 seconds.

## 1. Examine the code and the PR

Collect facts first. Never invent motivation or scope.

```sh
git branch --show-current
git status --short
gh pr view --json number,title,body,baseRefName,files,commits 2>/dev/null
git log --oneline <base>..HEAD
git diff --stat <base>...HEAD
```

Then read the diff itself (`git diff <base>...HEAD`) or the changed files, plus
the commit messages. Ignore generated files, lockfiles, and snapshots when
judging how large the PR is.

Extract:

- **Plan** — the workstream this PR belongs to (issue, ticket, branch name, commits).
- **Contribution** — what this PR specifically does toward that plan.
- **Changes** — what actually moved in the code, grouped by intent.
- **Tests** — test files added or modified and their test names.

Base branch: use the PR's `baseRefName`; otherwise `origin/main`, then
`origin/master`; ask only if still ambiguous.

## 2. Ask about the test suite before drafting

Call the `question` tool with one single-choice question:

- header: `Test suite`
- question: `What should the Test suite section contain?`
- options:
  1. `Describe test cases`
  2. `Describe testing scenario`
  3. `Empty`

Then follow the answer:

| Choice | Action |
| ------ | ------ |
| 1 | List tests found in the diff, max 3 bullets, format `- <test purpose>: <test name>` |
| 2 | Ask one short follow-up ("In one line: what did you do, and what did you verify?") and turn the answer into 1–2 bullets |
| 3 | Omit the `# Test suite` section entirely |

If the `question` tool is unavailable, present the same three options as a
numbered list in chat and wait for the reply.

## 3. Format

```markdown
# Why
- <one short sentence: the context this PR exists in — the plan, goal, or workstream. Omit this bullet if the PR is very small>
- <one short sentence: how this PR contributes to that plan>

# Idea
- <1–2 bullets on the approach behind the changes>

# Changes
- <change>
- <change>
- <change>

# Test suite
- <test purpose>: <test name>
```

- `# Why`: normally 2 bullets; a 3rd only when the PR is large or needs extra context.
- `# Idea`: only when the user asks for it.
- `# Changes`: exactly 2–3 bullets.

## 4. Style rules

- One line per bullet, at most ~20 words, no wrapping, no sub-bullets.
- Caps: Why ≤ 3, Idea ≤ 2, Changes ≤ 3, Test suite ≤ 3.
- Start each bullet with a verb or a concrete noun, never "This PR" or "We".
- No periods at the end of bullets; be consistent.
- Name files or modules only when they disambiguate; never re-narrate the diff.
- No emoji, no extra headings, no "Summary"/"Motivation" synonyms.
- Drop `# Why` entirely for a small, self-explanatory change; start at Changes.

## 5. Deliver

- Output the draft in one fenced `markdown` block, with at most one line of
  preamble.
- If a PR exists, offer to apply it: write the body to a temp file and run
  `gh pr edit <number> --body-file <file>`. Apply only after the user agrees.

## Example

```markdown
# Why
- Keep onboarding self-serve so support stops walking users through setup.
- Add a wizard that writes the same config the CLI already expects.

# Changes
- Add a 3-step onboarding wizard.
- Persist wizard answers to the shared config file.
- Validate answers with the existing config schema.

# Test suite
- Wizard happy path: onboarding-wizard.test.tsx
- Invalid step blocks next: wizard-validation.test.ts
```
