---
name: conventional-commit
description: Convert raw change descriptions or git diffs into properly formatted Conventional Commits messages. Use this whenever the user is about to write a commit message, asks to "format a commit", mentions "conventional commits", pastes a diff and asks what to commit it as, or says things like "what's a good commit message for this". Also trigger when the user has staged changes and asks for help writing the commit, even if they don't say "conventional".
---

# Conventional Commit Formatter

Turn a description of changes (or a diff) into a Conventional Commits message. Output is a single commit message block the user can paste into `git commit`.

## Format

```
<type>(<scope>): <subject>

<body>

<footer>
```

- **type** (required): one of `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`, `revert`.
- **scope** (optional): short noun identifying the area touched (e.g. `auth`, `api`, `deps`). Omit parentheses if no scope.
- **subject** (required): imperative, lowercase, no trailing period, ≤ 72 chars.
- **body** (optional): what and why, not how. Wrap at ~72 chars. Skip for trivial changes.
- **footer** (optional): `BREAKING CHANGE: <desc>` and/or issue refs like `Closes #123`.

## How to choose the type

| Change | Type |
|---|---|
| New user-facing capability | `feat` |
| Bug fix user can observe | `fix` |
| Docs only | `docs` |
| Whitespace/formatting, no logic | `style` |
| Code restructure, no behavior change | `refactor` |
| Measurable speed/memory improvement | `perf` |
| Adding/fixing tests | `test` |
| Build system, bundler, package manifest | `build` |
| CI config (GitHub Actions, CircleCI, etc.) | `ci` |
| Maintenance, version bumps, tool config | `chore` |
| Reverting a prior commit | `revert` |

When the change touches multiple types, pick the one that describes the **primary user-visible intent**. A `feat` that also adds tests is still `feat`.

## Process

1. Read the change description or diff the user gave you.
2. Identify the primary intent → pick one type.
3. Pick a scope if there's an obvious single subsystem. If the change spans many areas, drop the scope rather than inventing a vague one.
4. Write the subject in imperative mood ("add", "fix", "remove" — not "added", "adds", "adding").
5. Add a body only if the *why* isn't obvious from the subject.
6. Add `BREAKING CHANGE:` footer if public API / behavior changed incompatibly. Also append `!` after type/scope: `feat(api)!: ...`.
7. Output the full message in a fenced code block so it's easy to copy.

## Examples

**Example 1 — simple feat:**
Input: Added a dark mode toggle to the settings page.
Output:
```
feat(settings): add dark mode toggle
```

**Example 2 — fix with body:**
Input: The login form was crashing when users typed an email with a plus sign because the regex rejected it.
Output:
```
fix(auth): allow plus signs in email addresses

The address validation regex was rejecting RFC-compliant emails
containing `+`, causing the login form to reject valid accounts.
```

**Example 3 — breaking change:**
Input: Renamed the `/users` endpoint to `/accounts`. Old path no longer works.
Output:
```
feat(api)!: rename /users endpoint to /accounts

BREAKING CHANGE: the `/users` endpoint has been removed. Clients
must update to `/accounts`. No deprecation period is offered.
```

**Example 4 — multiple small changes, chore scope:**
Input: Bumped eslint, prettier, and typescript to latest minor versions.
Output:
```
chore(deps): bump eslint, prettier, typescript to latest minor
```

## Why imperative mood

Git's own conventions, and the Conventional Commits spec, treat the subject as completing the sentence *"If applied, this commit will ___"*. "add dark mode toggle" fits; "added dark mode toggle" does not. Keeping this consistent makes changelogs and `git log --oneline` read as a coherent list of actions rather than a mix of tenses.

## When in doubt

- Prefer `fix` over `chore` if a user could notice the change.
- Prefer no scope over a made-up scope.
- Prefer a short subject + body over a long subject.
- Never guess a type — if the intent is genuinely unclear from the input, ask the user one clarifying question before committing to a type.
