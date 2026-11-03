---
name: PR Branch
description: Create a work branch from the latest main, named `<user>/<TICKET>-<slug>` from a Linear ticket, check it out in its own worktree under the Paradev worktrees folder, and register a Paradev workspace for that worktree. Use when starting a ticket, creating branches, worktrees or Paradev workspaces, or when asked to branch off for a task.
---

# PR Branch

One fresh branch per ticket, cut from the latest base, checked out in its own
worktree and opened as a Paradev workspace. The current checkout stays where it
was. Never guess a ticket, a repo or a Paradev project, and never branch from a
stale local copy.

Paradev CLI: `superset` (alias `paradev`). Outside a Paradev terminal it may
not be on `PATH`; use `~/.superset/bin/superset` then.

## 1. Resolve the repo

| Situation | Repo |
| --------- | ---- |
| Working directory is a git repo | That repo |
| Not a repo, exactly one clone matches the ticket's project | That clone, and say which one |
| No match, or several candidates | Ask with the `question` tool |

Match on the `origin` URL and the project the ticket belongs to. Never guess
between repos. The Paradev project list (step 2) doubles as the list of clones.

Work against the repo's **main checkout**, even when the working directory is
one of its worktrees:

```sh
REPO=$(dirname "$(git rev-parse --path-format=absolute --git-common-dir)")
```

## 2. Resolve the Paradev project

Do this before creating anything, so a failure here leaves nothing half-made.

```sh
superset auth whoami --json
superset projects list --local --json
```

- **`whoami` fails** → stop and ask the user to run `! superset auth login`
  (opens a browser). The desktop app's login does not carry over to the CLI.
  Continue once it succeeds.
- **Exactly one project whose repo path is `$REPO`** → use it, and say which
  (name and id). Compare real paths.
- **No project for `$REPO`, several, or the request could belong to more than
  one project** → ask with the `question` tool: header `Paradev project`,
  question `Which Paradev project is this branch for?`. Offer the projects
  that point at the same GitHub repo, plus "no workspace (branch and worktree
  only)". Never pick one by name similarity alone.

## 3. Resolve the base

| Situation | Base |
| --------- | ---- |
| User named a base | That branch, as given |
| No base named | `main` |
| Remote has no `main` | The repo default branch (`master`), and say so |

Ask only when the repo has several plausible long-lived branches and no default.

## 4. Resolve the ticket

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

## 5. Name the branch

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

## 6. Create the branch

```sh
git -C "$REPO" fetch origin <base> --prune
git -C "$REPO" branch --no-track <branch> origin/<base>
```

**Never move the current checkout onto it**: no `git switch`, `git checkout
-b`, or anything else that changes the current branch. The branch is checked
out only in its new worktree (step 7).

- **Uncommitted or untracked files** → never a blocker, because nothing in the
  current checkout moves. Do not ask to commit or stash, and do not call
  untracked files "dirty"; mention them only if they matter to the task.
- **No `origin` remote** → branch from the local base instead.
- **Branch exists locally or on the remote** → do not reuse it silently; ask
  whether to use the existing branch for the worktree and workspace, or pick a
  different slug.

## 7. Create the worktree

```sh
WT=/Volumes/tf-ssd/hoanghl/.superset/worktrees/<project-name>/<branch>
git -C "$REPO" worktree add "$WT" <branch>
git -C "$REPO" config branch.<branch>.base <base>
```

- `<project-name>` — the Paradev project name from step 2 (`eva`,
  `tetra-gateway`, …). This is the folder Paradev uses for its own worktrees.
  With no Paradev project, use the repo folder name.
- `branch.<branch>.base` is where Paradev reads the base it diffs against.
- **`$WT` already exists** → stop and ask. Never reuse, overwrite or delete it.

## 8. Create the Paradev workspace

Skip this step if the user chose "no workspace" in step 2.

```sh
superset ws create --local --project <project-id> \
  --branch <branch> --skip-branch-prefix --json
```

- Paradev sees `<branch>` already checked out in `$WT` and adopts that
  worktree; it does not create a second one. `--skip-branch-prefix` keeps the
  branch name exact.
- Check that the returned workspace's worktree path is `$WT`. If it differs,
  report both paths; do not archive or recreate anything.
- If a workspace for the branch already exists, Paradev returns it. Report it
  as existing, not created.
- Do not pass `--agent` or `--prompt` unless the user asks for an agent.
- **Create fails** → keep the branch and worktree, report the error, and give
  the exact command to retry.

## 9. Report

Short lines plus the ticket link:

```text
Created hoanghl/tom-33-wire-protocol-codec from origin/main (3ad42fc)
Worktree: /Volumes/tf-ssd/hoanghl/.superset/worktrees/eva/hoanghl/tom-33-wire-protocol-codec
Paradev workspace <id> in project eva. Open it with: superset ws open <id> --local
```

Then stop: no commits, no push, no PR unless asked.
