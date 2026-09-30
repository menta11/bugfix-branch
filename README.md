# bugfix-branch

An agent skill that opens a bugfix branch safely — it checks the repo first, confirms the branch name with you, and never disturbs the work you already have in progress.

**English** · [简体中文](./README.zh-CN.md)

## What it is

Give it a bug description and it creates the branch `fix/<module>-<user>` — via a classic `git checkout -b` when the repo is clean and on the default branch, or in an isolated `git worktree` when the working tree is dirty or you are on another branch.

It only opens the branch. Fixing the bug, committing, pushing, opening a PR and merging are out of scope.

## Why it exists

`git checkout -b` is a one-liner that goes wrong quietly: uncommitted work gets dragged into the fix branch, `git stash` without `-u` leaves untracked files behind, branching off whatever happens to be checked out stacks the fix on top of an unrelated feature branch — and an agent-invented branch name won't match the `fix/<module>-<user>` convention.

So the skill front-loads the two things that are expensive to undo: **what state the repo is in**, and **what the branch will be called**.

## When it triggers

Only on an explicit request to open a bugfix / repair branch:

- "开个 bugfix 分支"
- "帮我拉个修复分支"
- "open a bugfix branch"
- "use bugfix-branch"

Not triggered by the word "bug" alone — a normal debugging session must not silently branch your repo.

## Installation

`bugfix-branch` is a plain Agent Skill — one `SKILL.md` in a folder named after the skill — so any agent that loads skills from a directory can use it.

The `skills` CLI installs it into the agent directories it detects:

```bash
npx skills add https://github.com/menta11/bugfix-branch.git --skill bugfix-branch
```

Or copy it in by hand. The example below uses Claude Code's directory (`~/.claude/skills/`) — substitute whatever directory your own agent scans:

```bash
git clone https://github.com/menta11/bugfix-branch.git
mkdir -p ~/.claude/skills/bugfix-branch
cp bugfix-branch/SKILL.md ~/.claude/skills/bugfix-branch/SKILL.md
```

On Windows (PowerShell):

```powershell
git clone https://github.com/menta11/bugfix-branch.git
New-Item -ItemType Directory -Force "$env:USERPROFILE\.claude\skills\bugfix-branch"
Copy-Item bugfix-branch\SKILL.md "$env:USERPROFILE\.claude\skills\bugfix-branch\SKILL.md"
```

## Example

```
You:   开个 bugfix 分支，导出报告的按钮点了没反应

Skill: Pre-flight (read-only) —
         working tree : dirty (2 modified, 1 untracked)
         current branch: feature/report-v2
         default branch: main

       Module slug : export-report
       User name   : (enter to accept lmt)
       Branch name : fix/export-report-lmt — confirm?

You:   确认

Skill: Dirty tree and not on the default branch → worktree recommended.
       Creating ../fix-export-report-lmt based on origin/main …
       Done. cd ../fix-export-report-lmt to start fixing.
       Your current branch and uncommitted changes were not touched.
```

## Repository layout

```
bugfix-branch/
  SKILL.md          # the skill itself — the only file your agent loads
  README.md         # this file
  README.zh-CN.md   # Chinese translation
```

Full flow, rules and edge cases: [SKILL.md](./SKILL.md).

## License

Released under the [MIT License](LICENSE) — © 2026 mengtao Liu.
