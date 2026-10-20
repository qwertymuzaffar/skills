# Udacity commit style: full rules

The complete rule set, as enforced by the
[mcp-udacity-commit](https://github.com/qwertymuzaffar/mcp-udacity-commit)
validator. SKILL.md has the short version; this file is the reference for
edge cases. Source: https://udacity.github.io/git-styleguide/

Lengths everywhere are counted in Unicode code points, so an emoji or a CJK
character counts as one.

## Message structure

```
type: Subject

Body

Footer
```

Three parts, each separated from the next by exactly one blank line. Only the
subject line is required. A message with a body but no blank line after the
subject is invalid.

## Types

| type       | meaning                                                                    |
| ---------- | -------------------------------------------------------------------------- |
| `feat`     | A new feature                                                              |
| `fix`      | A bug fix                                                                  |
| `docs`     | Changes to documentation                                                   |
| `style`    | Formatting, missing semicolons, etc; no code change                        |
| `refactor` | Refactoring production code                                                |
| `test`     | Adding tests, refactoring tests; no production code change                 |
| `chore`    | Updating build tasks, package configs, etc; no production code change      |

Anything else (`update`, `feature`, `bugfix`, `wip`, `misc`) is invalid.
Choose by effect on the codebase: a change that alters behavior for users is
`feat` or `fix`; one that leaves behavior identical is `refactor`, `style`,
`test`, `docs` or `chore`.

## Subject line

Format: `type: Subject` with a single space after the colon.

| rule                                                | failure or warning |
| --------------------------------------------------- | ------------------ |
| Matches `type: Subject`                             | failure            |
| Type is one of the seven above                      | failure            |
| Subject text is not empty                           | failure            |
| Subject begins with a capital letter                | failure            |
| No trailing period                                  | failure            |
| No trailing whitespace                              | failure            |
| Whole line, including `type: `, is 50 chars or less | failure            |
| First word is imperative, not past tense or gerund  | warning            |

The length limit applies to the whole line. `refactor: ` already uses 10 of
the 50 characters, so a refactor subject has 40 left.

### Imperative mood

Write the subject as a command: "Add", "Fix", "Remove", "Rename". The line
should complete the sentence "If applied, this commit will ...". The
validator warns on these first words (and their gerunds):

added, fixed, updated, changed, removed, deleted, created, refactored,
implemented, improved, renamed, moved, merged, reverted, bumped, cleaned,
corrected, adjusted, enabled, disabled, introduced, resolved, replaced,
converted, migrated, dropped, extracted, wrapped, tweaked, optimized,
simplified, formatted, documented, tested, released, handled, allowed,
prevented, ensured, avoided, unified, applied, upgraded, downgraded, patched,
hardened, restructured, reorganized, deprecated, exposed, integrated,
validated, normalized, cached, supported, wired, hooked

The list favors precision over recall: legitimate imperatives that happen to
end in "ed" or "ing" ("Embed", "Bring") are never flagged. Don't treat the
absence of a warning as proof the mood is right.

## Body

Optional. Include it when the subject alone would leave a reader asking why.

- Separated from the subject by one blank line.
- Every line is 72 characters or less. A longer line is a failure.
- Explains what changed and why, not how. The diff shows how.
- Paragraphs separated by blank lines and bullet points are fine.
- Long tokens such as URLs that cannot be wrapped still count; move them to
  the footer or break the line before them when possible.

## Footer

Optional. Separated from the body (or the subject, when there is no body) by
one blank line. Lines wrap at 72 characters like the body.

Issue references use one of these keywords followed by a colon and at least
one `#number`:

```
Resolves: #123
Closes: #45
Fixes: #7
See also: #456, #789
Refs: #12
```

Recognized keywords: `Resolves`, `Closes`, `Fixes`, `Fix`, `See also`,
`Refs`, `Ref`. A footer line that starts with one of these but has no
`#number` produces a warning.

Breaking changes are described in the footer as well, in plain prose.

## Branch names

A companion convention, not part of the official guide. Feature branches
are named after the change they carry, reusing the commit type set.

```
type/description
```

| rule                                                             | failure or warning |
| ---------------------------------------------------------------- | ------------------ |
| Contains a `/` separating type from description                  | failure            |
| Type is one of the seven commit types or `release`               | failure            |
| Description is not empty                                         | failure            |
| Description contains no further `/`                              | failure            |
| Description is kebab-case (lowercase letters and digits, single hyphens) | failure    |
| For `release/`, description is version-style (see below)         | failure            |
| No leading or trailing whitespace                                | failure            |
| Whole name is 50 characters or less                              | warning            |

Kebab-case means `^[a-z0-9]+(-[a-z0-9]+)*$`: no uppercase, no underscores,
no spaces, no leading, trailing or doubled hyphens.

`release/` branches take a version-style description instead, matching
`^[a-z0-9]+([.-][a-z0-9]+)*$`, so `release/1.2.0`, `release/2.0.0-rc1` and
`release/2024-q1` all pass.

Base branches `main`, `master`, `dev`, `develop` and `trunk` are exempt from
these rules. They are also the branches you should not commit on directly.

### Examples

```
feat/add-dark-mode
fix/duplicate-auth-refresh
chore/bump-deps
docs/api-reference
release/1.2.0
```

Invalid: `feature/add-dark-mode` (unknown type), `feat/Add_Dark_Mode`
(uppercase and underscore), `fix-login` (no slash), `feat/auth/sso` (second
slash).

## Validator tools

When the `udacity-commit` MCP server is connected these tools apply every
rule above and return a list of problems (fail) and warnings (pass with a
note):

| tool                      | input                                   |
| ------------------------- | --------------------------------------- |
| `validate_commit_message` | the full message text                   |
| `format_commit_message`   | `type`, `subject`, optional `body`, `footer`; returns a compliant message, capitalized and wrapped |
| `validate_branch_name`    | the branch name                         |

Install: `claude mcp add udacity-commit -- npx -y mcp-udacity-commit`
