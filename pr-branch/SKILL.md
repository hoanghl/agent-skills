---
name: PR Branch
description: Create a work branch from the latest main, named `<user>/<TICKET>-<slug>` from a Linear ticket, without checking it out. Use when starting a ticket, creating branches, or when asked to branch off for a task.
---

# PR Branch

One fresh branch per ticket, cut from the latest base, with `HEAD` left where it
was. Never guess a ticket, never branch from a stale local copy.

## 1. Resolve the repo

| Situation | Repo |
| --------- | ---- |
| Working directory is a git repo | That repo |
| Not a repo, exactly one clone matches the ticket's project | That clone, and say which one |
| No match, or several candidates | Ask with the `question` tool |

Match on the `origin` URL and the project the ticket belongs to. Never guess
between repos.

## 2. Resolve the base

| Situation | Base |
| --------- | ---- |
| User named a base | That branch, as given |
| No base named | `main` |
| Remote has no `main` | The repo default branch (`master`), and say so |

Ask only when the repo has several plausible long-lived branches and no default.

## 3. Resolve the ticket

- **Ticket code present** (in the request, branch name, or task) → read it with
  the Linear `get_issue` tool: `get_issue({ id: "TOM-33" })`. Use its title for
  the slug and its URL in the report.
- **No ticket and no task context** → stop and ask with the `question` tool:
  header `Linear ticket`, question `Which Linear ticket is this branch for?`,
  free-form answer expected.
- **Code does not resolve** → report the failed lookup and ask for the correct
  code. Never invent one.
- **Task context but no ticket** → ask once, offering "no ticket" as a choice.
  If the user confirms none, name the branch `<user>/<slug>` and state that it
  is not tracked to a ticket.

## 4. Name the branch

```
<user>/<TICKET>-<slug>
```

- `<user>` — `hoanghl`, unless the user asks for another prefix.
- `<TICKET>` — the code, lowercased: `tom-33`.
- `<slug>` — from the ticket title, 3–5 words.

Slug rules:

- Lowercase ASCII, words joined by `-`, `^[a-z0-9]+(-[a-z0-9]+)*$`.
- Drop prefixes (`Step 1:`, `[FE]`, `feat:`), stopwords (`the`, `a`, `an`,
  `of`, `for`, `to`, `in`, `on`, `and`, `with`), and version noise.
- Keep the nouns and verbs that name the work; 3–5 words, 40 characters max.

Example: TOM-33 "Step 1: Wire protocol and codec" →
`hoanghl/tom-33-wire-protocol-codec`.

## 5. Create it — do not check it out

```sh
git fetch origin <base> --prune
git branch --no-track <branch> origin/<base>
```

**Create the branch only.** Never move onto it: no `git switch`, `git checkout
-b`, or anything else that changes the current branch. Leave the current branch
and working tree exactly as they were; the user switches when ready.

- **Uncommitted or untracked files** → never a blocker, because creating a
  branch moves nothing. Do not ask to commit or stash, and do not call untracked
  files "dirty"; mention them only if they matter to the task.
- **No `origin` remote** → branch from the local base instead.
- **Branch exists locally or on the remote** → do not reuse it; ask whether to
  switch to it or pick a different slug.

## 6. Report

One line plus the ticket link:

```text
Created hoanghl/tom-33-wire-protocol-codec from origin/main (3ad42fc)
Switch to it with: git switch hoanghl/tom-33-wire-protocol-codec
```

Then stop: no commits, no push, no PR unless asked.
