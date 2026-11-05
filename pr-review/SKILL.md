---
name: PR Review
description: Review a pull request with two independent sub-agent reviewers run by a master agent — list the review aspects, collect both reports, run up to 3 deep-dive rounds, then write a sectioned review (workstream, where the PR fits, design, related work and docs, issues per reviewer, test suite, code length, edge cases) with diagrams and tables where they explain faster. Also composes the response for the PR author with a decision (request changes, comment, approve). Use when asked to review, assess, or explain a PR in depth, or to compose a review response.
---

# PR Review

You are the master. Two sub-agents review the same PR independently. You
compare their reports, send them back to dig where they disagree or stay thin,
and write the final review. Reviewers only read. Nothing here edits code, and
nothing is posted to the PR unless the user confirms (§9).

## Commands

| Command | User says | Does |
| ------- | --------- | ---- |
| Review (default) | "review PR …", a PR link | §1–§7: two reviewers, deep dives, the 8-section review |
| Compose the response | "compose the response", "draft the review message" | §8: the message for the PR author, plus a decision |
| Post | "post it" | §9: posts the composed response, after the user confirms |

```text
1. Master                           resolve the PR, list the aspects        §1–§2
2. Master  ──same brief──▶  A, B    review in parallel                      §3
3. A, B    ──report──────▶  Master  compare in a ledger                     §4
4. Master  ──deep dive───▶  A / B   at most 3 rounds, optional              §5
5. Master                           write the 8-section review              §6–§7
6. Master                           compose the response and decision       §8, on request
```

## What the review covers

The final review always has these eight sections, in this order. The reviewers
report on the same eight.

| # | Section | Answers | Content | Visual that fits |
| - | ------- | ------- | ------- | ---------------- |
| 1 | Workstream | What larger effort is this PR part of? | Project, initiative or epic; the problem and its early signals; the goal | Rarely needed |
| 2 | Where this PR fits | Which step is it, and what does it unblock? | Step N of M; PRs before and after; the part of the problem it solves | PR dependency flowchart, or a step table |
| 3 | Design | How does it work? | Approach, components, data flow, key decisions, trade-offs | Sequence diagram, flowchart, or before/after table |
| 4 | Related work and docs | What else should a reader open? | PRs in this or other repos with their state; tickets; RFCs, `ARCHITECTURE.md`, plans | Table: item, state, relation |
| 5 | Implementation issues | What is wrong, and who found it? | Issues grouped by reviewer, with severity, location and status; dropped findings | None: grouped bullets only |
| 6 | Test suite | What proves it works, and what does not? | Tests added or changed and what they cover; CI result; untested paths | Table: test, covers, source |
| 7 | Code length | Is the PR sized for one review? | Changed lines by kind (code, tests, config, docs, data); generated files; what could shrink or split | Table: kind, files, lines |
| 8 | Edge cases | Which boundary inputs and states does the change meet, and are they handled? | Empty or missing input, limits, concurrency, partial failure, restart, records from before the change | Table: edge case, handled, tested |

## 1. Resolve the PR

| Input | PR |
| ----- | -- |
| Number | That PR in the current repo |
| URL or `owner/repo#n` | That PR |
| Nothing | The current branch's PR (`gh pr view`) |
| No PR found | Ask with the `question` tool; never guess |

```sh
gh pr view <n> -R <owner/repo> --json number,title,body,state,isDraft,author,baseRefName,headRefName,headRefOid,additions,deletions,files,commits,url
gh pr checks <n> -R <owner/repo>
```

- **`gh` fails on auth** → stop and ask the user to run `! gh auth login`.
- **Ticket**: from the branch name (`<user>/<team>-<num>-<slug>`) or the PR
  body. Read it with Linear `get_issue`, plus its parent issue and project.
- **Code**: in a local clone, `git fetch origin pull/<n>/head` and read files
  with `git show <headRefOid>:<path>`. No clone → `gh pr diff` and
  `gh api repos/<owner>/<repo>/contents/<path>?ref=<headRefOid>`.
- **Size**: sum `files[].additions/deletions` by kind (code, tests, config,
  docs, data). Name generated files, lockfiles, snapshots, vendored code and
  large data files separately; they do not count toward review size.
- Never switch, reset, or check out over the user's working tree.

## 2. List the aspects

Write the review plan before spawning anything.

- **Fixed aspects**: the eight sections above. Always all eight.
- **Focus areas**: 3–6 risks specific to this PR, checked under aspects 5 and
  8. Pick them from the diff:

| Diff touches | Focus area |
| ------------ | ---------- |
| Public API, schema, message format | Contract, backward compatibility |
| DB schema, data migration | Migration safety, rollback |
| Async, threads, locks, retries | Concurrency, cancellation, idempotency |
| External calls, error paths | Swallowed errors, missing timeouts, unbounded retries |
| Auth, secrets, user input | Security |
| Hot paths, I/O in loops, caches | Performance |
| New helpers, clients, utilities | Reuse: existing code that already does it |
| Spans, metrics, logs | Label cardinality, missing signals |
| Terraform, config, flags | Plan output, environment parity, rollout |
| Behaviour or structure | Docs that must change with it (`ARCHITECTURE.md`, README) |

Print the plan in one line, then continue without waiting for approval:
`Reviewing tetra#412 (head 3f9c2e1, +320/−45 in 9 files), focus: migration safety, retry idempotency, span cardinality`.

## 3. Spawn two independent reviewers

- Spawn Reviewer A and Reviewer B in one message so they run in parallel.
  Claude Code: `Agent` with `general-purpose`, never `fork` (a fork inherits
  your context). opencode: `task`.
- Give both the same brief. Only the reviewer letter differs. Separate contexts
  give two independent opinions, so agreement between them is a signal.
- The brief holds facts only. Leave out your hypotheses and suspected bugs:
  a reviewer handed a hypothesis tends to confirm it.
- Neither reviewer sees the other's report.
- Keep both agent ids. Every follow-up goes to the same agent (§5).

Brief:

```text
You are Reviewer <A|B>, one of two independent reviewers of <owner/repo>#<n> "<title>".
Head <headRefOid>, base <baseRefName>. Ticket: <ID, or "none found">.
PR body: <verbatim when short; otherwise "read it with gh pr view">.
Size: <+additions/−deletions in N files>; generated or data files: <list, or "none">.
Read code with: <git show <headRefOid>:<path> in <clone path> | gh api .../contents/<path>?ref=<headRefOid>>.
Diff: <git diff origin/<baseRefName>...<headRefOid> in <clone path> | gh pr diff <n> -R <owner/repo>>.

Rules:
- Read only. No edits, commits, pushes, PR comments or reviews. Never check out, switch or reset the clone.
- Do not run tests; read CI with `gh pr checks` ("no checks reported" means CI: none).
- Inspect large data or generated files with scripts or jq, not by reading them whole.
- Read the repo's CLAUDE.md, AGENTS.md and ARCHITECTURE.md first; their rules are review criteria.
- Load installed skills that cover this code's area (for example service-gotchas for HTTP, DB or telemetry code).
- Cite every fact: `path:line` at the head SHA, `repo#n`, a ticket id, or a URL. Put uncited claims under Open questions.
- Every issue needs a concrete failure scenario. Skip style nits that a linter or formatter would catch.
- An unhandled edge case with a failure scenario is also an issue under aspect 5.
- Related work: search the ticket id and branch slug in this repo and the org (`gh search prs --owner <org> "<ticket>"`),
  the ticket's attachments, parent and sibling issues, and links in the PR body.

Aspects: 1 Workstream, 2 Where this PR fits, 3 Design, 4 Related work and docs, 5 Implementation issues,
6 Test suite, 7 Code length, 8 Edge cases.
Focus areas for aspects 5 and 8: <list>.

Severity:
<severity table>

Report in the template below, at most ~100 lines.
<report template>
```

Report template:

```markdown
## 1. Workstream
- <cited bullets>
## 2. Where this PR fits
- <what came before, what this PR adds, what comes next>
## 3. Design
- <approach, components, trade-offs>
## 4. Related work and docs
- <repo#n> — <title> — <open | draft | merged | closed>
- <doc> — <what it covers>
## 5. Implementation issues
- [high|medium|low] <defect> — `path:line` — <inputs/state → wrong result> — confidence <high|medium|low>
## 6. Test suite
- <test name> — <what it covers>
- CI: <pass | fail | pending | none>; gaps: <untested paths>
## 7. Code length
- <lines by kind>; <can it shrink (dead code, duplication, unused options) or split, and where>
## 8. Edge cases
- <edge case> — handled: <yes `path:line` | no> — tested: <test name | no>
## Open questions
- <what you could not verify, and what would settle it>
```

Severity table, pasted into the brief so both reviewers use one scale. It says
what the reader should do about the issue:

| Severity | Meaning | Action |
| -------- | ------- | ------ |
| high | Wrong result, data loss, security hole, or outage on a realistic path | Fix before merge |
| medium | Bug on an edge path, or a missing guard with a plausible trigger | Fix, or say why not |
| low | Maintainability or clarity | Optional |

## 4. Compare the reports

After every round, update a ledger for yourself. It is not part of the output.

| Item | Reviewer A | Reviewer B | Status |
| ---- | ---------- | ---------- | ------ |
| A fact or an issue | what A says | what B says | settled / conflict / one-sided / thin |

- **Settled**: both reviewers agree and cite it, or a deep dive verified it.
- **Conflict**: the reviewers state different facts.
- **One-sided**: only one reviewer reported the issue.
- **Thin**: an aspect with no cited bullets, or only Open questions.
- You may open a cited line yourself to settle a trivial point. Anything that
  takes more than a few reads goes to a reviewer.

## 5. Deep-dive rounds (at most 3)

Run a round only when the ledger has conflicts, one-sided issues of medium
severity or higher, or thin aspects. Otherwise go to §6.

- **Ask**: name the aspect, one concrete question, where to start, and the
  answer format: `verdict: confirmed | refuted | unclear` plus cited evidence.
- **Split**: give A and B different aspects in the same round so they dig in
  parallel.
- **Cross-check**: send a one-sided issue to the reviewer who did not report
  it. Phrase it as a neutral question about location and trigger, without the
  other reviewer's reasoning or verdict:
  `Can retry() at client.py:88 send the same charge twice if the first call times out after the provider accepted it? Look for evidence either way.`
- Send all asks of a round in one message. Continue the same agents:
  Claude Code `SendMessage` to the agent id, opencode `task` with its
  `task_id`. If the harness cannot continue an agent, start a fresh one with
  the original brief, that reviewer's last report, and the question.
- One round = the asks sent plus their answers back. Update the ledger (§4)
  after each round.
- Stop after round 3. An issue still one-sided is `unverified`; a fact or issue
  still in conflict is `disputed`.

## 6. Write the review

```markdown
# 1. Workstream
- <bullet>

# 2. Where this PR fits
- <bullet>
<optional visual>

# 3. Design
- <bullet>
<optional visual>

# 4. Related work and docs
- <bullet>
<optional visual>

# 5. Implementation issues
**Both reviewers**
- [<severity>] <issue> (<source>) — confirmed

**Reviewer A only**
- [<severity>] <issue> (<source>) — <confirmed by B | disputed by B | unverified>

**Reviewer B only**
- [<severity>] <issue> (<source>) — <confirmed by A | disputed by A | unverified>

**Dropped**
- <issue> (Reviewer <X>): <why: refuted in a deep dive, or accepted by an agreed plan>

# 6. Test suite
- <bullet>
<optional visual>

# 7. Code length
- <bullet>
<optional visual>

# 8. Edge cases
- <bullet>
<optional visual>
```

Bullets:

- All eight sections, in this order, with these headings. When a section has
  nothing, give one bullet that says what was searched:
  `- No related PRs found (searched tetra and the org for TET-88)`.
- The first bullet answers the section's question directly. 2–5 bullets per
  section.
- One line per bullet, at most ~20 words (issue bullets up to ~30), no
  sub-bullets. Start with a noun or verb, never "This PR", "We" or "Basically".
- Section 5: keep who found each issue. Leave out empty groups, sort each
  group by severity, show at most 8 issues in total, and sum up the rest in one
  bullet. No issues at all → `- No issues found by either reviewer`.
- Section 6: if no test file changed and the PR changes more than ~50 lines of
  code, say so in the first bullet.
- Section 7: the first bullet gives `+<added>/−<deleted> in <n> files` without
  generated or data files, and names those separately. Over ~400 changed lines
  of code (tests and data excluded), say where it could split. Name code that
  could go: dead code, duplication, options nothing uses.
- Section 8: the 3–6 edge cases that matter most for this change. An unhandled
  one that is already an issue points to section 5 instead of repeating it.
- No verdict, no summary, no headings beyond the eight. The decision comes only
  from §8, on request.

Visuals:

- Use a diagram or table wherever it explains faster than bullets. The
  "Visual that fits" column above suggests the form for each section.
- Format, placement, size limits, PR dependency colors and the `Source:` line
  all follow `analysis-description` §5. In short: at most one visual per
  section, right after its bullets; ASCII in Terminal mode and Mermaid in MD
  file mode; about 10 nodes or 6 rows at most; real names on every node and
  cell.
- Draw only from facts in the ledger. Skip the visual when the bullets already
  say everything.

Citations follow `analysis-description` §6, at the PR head SHA. Inferences
carry no citation.

## 7. Deliver

| Mode | When | Delivery |
| ---- | ---- | -------- |
| Terminal | Default | The review as the reply body, never in a code fence. At most one line of preamble: rounds run, and what stayed unresolved |
| MD file | User asks for a file | Write to the path the user names; otherwise `<mmdd>-<repo>-pr-<n>-review.md` in the workspace `artifacts/` folder, asking when unclear. Reply with the path |

Terminal and MD file modes differ in citations and diagrams exactly as in
`analysis-description` §3.

## 8. Compose the response

The message for the PR author, plus a decision. It is built from the finished
review. No review in the conversation yet → say in one line that you are
running it first, then run §1–§6.

````markdown
# 1. What this PR does
- <the big general plan>
- <how this PR helps that plan>
- <the implementation idea of this PR>

# 2. What are the problems
- <problem>
- <problem>
````

- Keep the headings verbatim. Section 1 has exactly 3 bullets, in this order.
- Section 2 lists confirmed issues from review section 5, most severe first.
  Leave out dropped, unverified and disputed ones; an unverified or disputed
  issue of medium or higher may go in as a question.
- A dependency outside the PR, such as a change another repo must ship first,
  goes in as an ask: `hold the prod release until <change> is live`.
- One short sentence per bullet, at most ~25 words. Name what to change and
  where: the full repo-relative path and line at the head SHA; another repo's
  file as `<repo> <path>:<line>`.
- Write for the author: no Linear ids or links, no private docs or plan names,
  no "Reviewer A/B", no severity tags.
- No problems → `- None found`.

Decision: pick one.

| Decision | When |
| -------- | ---- |
| Request changes | A confirmed high issue that this PR's own code must fix before merge |
| Comment | No such issue, but a confirmed medium that needs a fix or the author's reason, an open question, or a release that must wait on another change |
| Approve | Only low issues or none, and nothing that needs the author's answer |

- The user is the PR's author → `Comment`; GitHub allows neither approving nor
  requesting changes on your own PR.
- The decision is a recommendation; the user makes the call.

Output: the message in one fenced `markdown` block, then the decision with 1–2
bullets for the user: why, and, unless it is Approve, what would turn it into
Approve. Then offer to post it (§9).

## 9. Post to the PR

Only when the user asks, and only after they confirm the exact text.

1. Write the composed response (§8) to a file. It is the review body; the
   decision is the event.
2. Optional inline comments, one per confirmed issue on a line inside the diff
   (GitHub rejects other lines): 1–2 plain sentences naming what to change.
   Use `side=RIGHT` for added or context lines, `LEFT` for deleted ones. Add a
   ```` ```suggestion ```` block only for a small fix you are sure of; use 4
   backticks when the suggested code has fences of its own.
3. Show the body, every inline comment (`path:line` and text), and the event,
   then ask yes/no with the `question` tool. Agreeing with the review is not
   approval to post it.
4. After a yes, post.

Body only:

```sh
gh pr review <n> -R <owner/repo> <--comment | --approve | --request-changes> --body-file <file>
```

With inline comments: create one pending review, then submit it, so the
author gets one notification.

```sh
gh api repos/<owner>/<repo>/pulls/<n>/reviews -X POST \
  -f commit_id=<headRefOid> \
  -f 'comments[][path]=<path>' -F 'comments[][line]=<line>' -f 'comments[][side]=RIGHT' \
  -f 'comments[][body]=<comment>' \
  --jq '{id, state}'
gh api repos/<owner>/<repo>/pulls/<n>/reviews/<review id>/events -X POST \
  -f event=<COMMENT | APPROVE | REQUEST_CHANGES> -F body=@<file>
```

- Repeat the four `comments[][…]` flags once per comment. Quote them in single
  quotes, and pass numbers with `-F`.

## Example

Review, Terminal reply for `acme/orders-api#214` "Add idempotency keys to
payment retries". The outer fence only delimits the example; the real reply has
none.

````markdown
# 1. Workstream
- Payments reliability: stop duplicate charges when the provider times out (PAY-40)
- Peak-hour timeouts caused 37 duplicate charges in October (PAY-40)

# 2. Where this PR fits
- Step 2 of 3: adds idempotency keys after the pool-size fix (orders-api#201)
- Unblocks step 3, retiring the manual refund script (PAY-43)

# 3. Design
- One key per checkout, reused on every retry (`retry.py:41`)
- Keys live in Redis for 24 h, matching the provider's dedupe window (`keys.py:12`)
- A Redis outage turns dedupe off instead of blocking checkout (`keys.py:30`)

```text
1. checkout   ──new key──────────▶  Redis       SET key, TTL 24 h
2. checkout   ──POST /charges────▶  provider    Idempotency-Key: key
3. provider   ──timeout──────────▶  checkout    retry() reuses the same key
4. checkout   ──POST /charges────▶  provider    deduped, no second charge
```

Source: `retry.py:41-57`, `keys.py:12-30`

# 4. Related work and docs
- orders-api#201: HTTP pool 10 → 50, merged
- payments-gateway#88: forwards the key header upstream, in review
- `ARCHITECTURE.md` §Payments still describes retries without keys

# 5. Implementation issues
**Both reviewers**
- [high] A worker restart mid-retry regenerates the key, so duplicates stay possible (`retry.py:57`) — confirmed

**Reviewer A only**
- [medium] Redis `SET` lacks `NX`, so concurrent retries overwrite each other's key (`keys.py:18`) — confirmed by B

**Reviewer B only**
- [low] TTL is hard-coded; the repo keeps timeouts in `settings.py` (`keys.py:12`) — unverified

**Dropped**
- Key leaks into logs (Reviewer A): the logger masks the field (`log.py:22`)

# 6. Test suite
- Covers key reuse and expiry; nothing restarts the worker between attempts
- CI passes on head 9e4b1c2

| Test | Covers | Source |
| ---- | ------ | ------ |
| `test_retry_reuses_key` | Same key on every retry | `test_retry.py:12` |
| `test_key_expires` | Key gone after TTL | `test_retry.py:31` |

# 7. Code length
- +146/−12 in 5 files, no generated files; fits one review pass
- `keys.py` repeats the Redis client setup from `cache.py` (`keys.py:5-14`)

| Kind | Files | Lines |
| ---- | ----- | ----- |
| Code | 3 | +98/−12 |
| Tests | 2 | +48/−0 |

# 8. Edge cases
- Restart mid-retry and concurrent retries are unhandled; both are issues in section 5

| Edge case | Handled | Tested |
| --------- | ------- | ------ |
| Retry after timeout | yes, `retry.py:41` | `test_retry_reuses_key` |
| Key expired before retry | yes, `keys.py:30` | `test_key_expires` |
| Worker restart mid-retry | no, see section 5 | no |
| Concurrent retries | no, see section 5 | no |
| Redis down | yes, dedupe off, `keys.py:30` | no |
````

Compose the response, for the same PR:

````markdown
```markdown
# 1. What this PR does
- Payments reliability: stop duplicate charges when the provider times out
- Second of three steps, after the pool-size fix; unblocks retiring the manual refund script
- Reuses one idempotency key per checkout across retries, stored in Redis for the provider's 24 h window

# 2. What are the problems
- A worker restart mid-retry generates a new key, so duplicates are still possible (`orders/payments/retry.py:57`)
- `SET` without `NX` lets concurrent retries overwrite each other's key; use `SET NX` (`orders/payments/keys.py:18`)
```

**Decision: Request changes**
- A confirmed high issue in this PR's own code (`retry.py:57`)
- Approve once the key survives a worker restart and the `SET` uses `NX`
````
