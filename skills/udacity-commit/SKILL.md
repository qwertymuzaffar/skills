---
name: udacity-commit
description: >-
  Write git commit messages and branch names in the Udacity Git Commit Message
  Style Guide format: "type: Subject" with a capitalized imperative subject of
  at most 50 characters, a body wrapped at 72, and feature branches named
  type/kebab-case off a clean base branch. Use this whenever the user asks to
  commit, stage, save or check in work, write or reword a commit message, amend
  a commit, create or name a branch, or mentions Udacity style or a git commit
  style guide, even if they never say "Udacity" and even for a one-line fix.
license: MIT
metadata:
  author: qwertymuzaffar
  version: "1.0.0"
---

# Udacity commit style

Commit messages are read far more often than they are written: in `git log`,
in `git blame`, in code review, and by whoever is debugging at 3 a.m. The
[Udacity Git Commit Message Style Guide](https://udacity.github.io/git-styleguide/)
keeps them scannable with one consistent shape. This skill applies it, plus a
companion convention for naming branches, every time work is committed.

This is not Conventional Commits: there is no `(scope)`, the subject is
Capitalized, and the types are limited to the seven below. Don't mix the two.

## Workflow

1. **Check the branch.** Run `git branch --show-current`. If it is a base
   branch (`main`, `master`, `dev`, `develop`, `trunk`), create a feature
   branch first with `git checkout -b type/kebab-case-description`.
   Uncommitted changes carry over. Base branches stay clean and every change
   arrives as a reviewable unit, so don't commit on one unless the user says
   to.
2. **Stage deliberately.** Add the files that belong to this change by path,
   not with `git add -A`. Leave out local-only files (env pointers, scratch
   files, unrelated lockfile churn) and tell the user what you left out.
3. **Write the message** in the format below. Pick the type from what the
   diff actually does, not from what the user called it.
4. **Validate before committing.** If the `udacity-commit` MCP server is
   connected, call `validate_commit_message` (or `format_commit_message` to
   build the message from parts) and `validate_branch_name`, and fix anything
   it reports. Without it, check the message against the rules by hand; the
   limits are exact, so count. The server installs with
   `claude mcp add udacity-commit -- npx -y mcp-udacity-commit`.
5. **Commit with the message intact.** Use a heredoc or `-F` so the blank
   lines survive:

   ```bash
   git commit -F - <<'EOF'
   type: Subject

   Body.
   EOF
   ```

6. **Stop there.** Committing is local and reversible; pushing and opening a
   pull request are not. Do neither unless the user asks.

## Message format

```
type: Subject

Body (optional)

Footer (optional)
```

| type       | use for                                                          |
| ---------- | ---------------------------------------------------------------- |
| `feat`     | a new feature                                                    |
| `fix`      | a bug fix                                                        |
| `docs`     | documentation only                                               |
| `style`    | formatting, whitespace, semicolons; no code change               |
| `refactor` | production code change that is neither a fix nor a feature       |
| `test`     | adding or refactoring tests; no production code change           |
| `chore`    | build tasks, package configs, tooling; no production code change |

**Subject line** (`type: Subject`):

- The whole line, including the `type: ` prefix, is at most 50 characters.
- The subject begins with a capital letter and has no trailing period.
- Imperative mood: "Add", "Fix", "Remove", not "Added", "Fixes", "Removing".
  A good test: the line should complete "If applied, this commit will ...".
- Says what the commit does, not what you did.

**Body**, only when the change needs explaining:

- One blank line after the subject.
- Lines wrap at 72 characters.
- Explains what changed and why. The diff already shows how.
- Paragraphs and bullet points are fine.

**Footer**, only when there is something to reference:

- Issue references: `Resolves: #123`, `Closes: #45`, `See also: #456, #789`.
- Breaking changes are noted here too.
- Keep it to references; don't add tooling trailers the project doesn't use.

## Branch names

```
type/kebab-case-description
```

- `type` is one of the seven commit types, or `release`.
- The description is lowercase letters and digits joined by single hyphens:
  `feat/add-dark-mode`, not `feature/Add_Dark_Mode`.
- `release/` branches take a version instead: `release/1.2.0`,
  `release/2024-q1`.
- Keep the whole name to about 50 characters.
- Base branches (`main`, `master`, `dev`, `develop`, `trunk`) are exempt.

## Examples

```
feat: Add mobile fullscreen appointment dialogs
fix: Reset patient selection when name is edited
refactor: Consolidate user permissions into staff
docs: Document the staff permission migration
chore: Bump TypeScript to 5.6
```

A complete message:

```
fix: Prevent duplicate auth token refresh

The refresh timer could fire twice under load, minting two tokens and
logging the user out. Serialize refreshes behind a single in-flight
promise so concurrent callers await the same request.

Resolves: #142
```

Common misses, and the fix:

| wrong                                                    | why                           | right                                |
| -------------------------------------------------------- | ----------------------------- | ------------------------------------ |
| `Fixed the login bug.`                                   | no type, past tense, period   | `fix: Handle expired login sessions` |
| `feat: added dark mode`                                  | lowercase, past tense         | `feat: Add dark mode`                |
| `feat(auth): Add SSO`                                    | scope is Conventional Commits | `feat: Add SSO login`                |
| `update: Change config`                                  | not a valid type              | `chore: Change CI config`            |
| `refactor: Rework the whole data access layer for speed` | 54 chars, over 50             | `refactor: Rework data access layer` |

The full rule text, including the past-tense word list and the footer
keywords the validator recognizes, is in
[references/style-guide.md](references/style-guide.md). Read it when a case
isn't covered here.
