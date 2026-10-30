---
name: Analysis Description
description: Format finished research as one numbered section per question — `# N. <Question>` followed by short, cited bullets, plus a diagram or table where it explains faster than text. Use after the research is done, when the user lists the questions to describe.
---

# Analysis Description

Turn research that is already done into answers a reader can scan. One heading
per question, short bullets under it, a source on every fact.

## 1. Check the inputs

Both inputs come from the user:

- **Research**: findings in the conversation, or notes, docs, and files the user
  points to.
- **Questions**: a numbered list, `(1) …`, `(2) …`.

| Situation | Action |
| --------- | ------ |
| Research and questions present | Go to §2 |
| No questions | Ask with the `question` tool for the list; never guess what to answer |
| No research yet | Stop and say the research comes first; do not start it in this skill |
| Research misses a question | Keep its heading with one bullet `- Not covered by the research: <what is missing>` |

Allowed: reopening a file or page the research already found, to pin the exact
line or URL for a citation. Not allowed: new investigation.

## 2. Map the questions

- Keep the user's numbering and order: one section per question.
- Keep the question text as given; fix only obvious typos.
- Never merge, split, reorder, or add questions.

## 3. Pick the output mode

| Mode | When | Output |
| ---- | ---- | ------ |
| Terminal (default) | Reply in the session | Rendered in the reply: short plain citations, no links or URLs, ASCII diagrams |
| MD file | User asks to print or write the description to an MD file | Raw Markdown in the file: link citations with permalinks, Mermaid diagrams |

The structure (§4) is the same in both; visuals (§5), citations (§6), and
delivery (§7) follow the mode.

## 4. Format

```markdown
# 1. <Question 1>
- <item>
- <item>

# 2. <Question 2>
- <item>
```

- The first bullet answers the question directly; the rest support it.
- 2–5 bullets per question.
- One short sentence per bullet: one line, at most ~20 words, no sub-bullets.
- Start with a noun or verb, never "This", "We", or "Basically".
- No intro, summary, or conclusion; no headings besides the questions.

## 5. Add a visual when it explains faster than bullets

| Idea | Visual |
| ---- | ------ |
| Request flow, call order, handoffs | Sequence diagram |
| Pipeline, architecture, dependencies | Flowchart |
| Lifecycle, status transitions | State diagram |
| Options, before/after, per-environment values | Table |
| Code or directory layout | Tree |

- Place it right after the bullets of the question it serves; at most one per
  question.
- Diagrams: ASCII in a `text` block in Terminal mode; Mermaid in MD file mode.
- Tables: a plain Markdown table, never inside a code fence. Cells that cite use
  the mode's citation form (§6).
- Terminal tables: at most 4 columns and short cells, so rows fit the terminal
  width. When every cited file is in one repo, name it once in the header:
  `Source (orders-api)`.
- Every node, arrow, and cell comes from the research, labeled with real names
  (services, files, fields).
- Keep it small: about 10 nodes or 6 rows at most. Bigger → split or drop detail.
- Skip it when the bullets already say everything.

## 6. Cite facts

A bullet that restates an existing fact ends with its source in parentheses. A
bullet that is your inference or recommendation carries none.

| Fact from | Terminal | MD file |
| --------- | -------- | ------- |
| Code | `` `client.py:31` in orders-api `` | `[client.py#L31](https://github.com/<org>/<repo>/blob/<sha>/<path>#L31)` |
| Repo doc | `` `ARCHITECTURE.md` §Sessions `` | `[ARCHITECTURE.md §Sessions](<permalink>)` |
| Web page or external doc | Page title, or the short URL without `https://` | `[Page title](url)` |
| PR, issue, ticket | `orders-api#123` | `[orders-api#123](url)` |
| Live data (query, log, metric) | Dashboard or query name, plus the time window | `[Dashboard](url)`, plus the time window |

- Terminal: no Markdown links and no full URLs; the source should read at a
  glance.
- Terminal code: file name and line, plus `in <repo>` when it is not the current
  repo. Two files share a name → use the shortest path tail that tells them
  apart.
- MD file: permalinks pinned to a full commit SHA, so links survive later
  commits.
- A visual built from facts gets one `Source:` line under it, in the mode's form.
- Cite only what you actually opened, with line numbers checked against the
  current file.
- A fact you cannot source → reopen it and cite, or drop the bullet. Never
  invent a citation.
- At most two sources per bullet; prefer the most direct one.

## 7. Deliver

| Mode | Delivery |
| ---- | -------- |
| Terminal | At most one line of preamble, then the description as the reply body itself, never wrapped in a code fence |
| MD file | Write to the path the user names; otherwise `<mmdd>-<topic>.md` next to the research docs, asking when unclear. Reply with one line: the file path |

The preamble line is for naming a question the research missed, nothing else.

## Example

Questions:

```text
(1) Why do checkout requests time out at peak traffic?
(2) Which setting should change first?
```

Terminal reply (the outer fence only delimits the example; the reply has none):

````markdown
# 1. Why do checkout requests time out at peak traffic?
- Payment calls queue for a connection because the HTTP pool caps at 10 (`client.py:31` in orders-api)
- Peak traffic keeps about 40 payment calls in flight (Grafana "Payments in-flight", 10-28 14:00–15:00 UTC)
- The 5 s client timeout includes queue time, so queued calls expire before reaching the provider (`client.py:34` in orders-api)

```text
1. orders-api        ──acquire──────────▶  HTTP pool (max 10)   waits while all 10 are busy
2. HTTP pool         ──POST /charges────▶  Payment provider
3. Payment provider  ──200 after ~1.2 s─▶  orders-api
```

Source: `client.py:31-34` in orders-api

# 2. Which setting should change first?
- Raise the pool size first; it removes the queue without hiding provider latency
- Size it to 50 to cover the 40 in-flight peak with headroom
- Keep the 5 s timeout; the provider's p99 is 1.8 s (status.example.com)

| Setting | Now | Proposed | Source (orders-api) |
| ------- | --- | -------- | ------------------- |
| `max_connections` | 10 | 50 | `client.py:31` |
| `timeout_s` | 5 | 5 | `client.py:34` |
````

The same first bullet and table row in MD file mode:

```markdown
- Payment calls queue for a connection because the HTTP pool caps at 10 ([client.py#L31](https://github.com/acme/orders-api/blob/4f2c9e1b7a0d3c5e8f6a2b1c9d0e7f3a5b8c4d2e/orders/payments/client.py#L31))

| `max_connections` | 10 | 50 | [client.py#L31](https://github.com/acme/orders-api/blob/4f2c9e1b7a0d3c5e8f6a2b1c9d0e7f3a5b8c4d2e/orders/payments/client.py#L31) |
```
