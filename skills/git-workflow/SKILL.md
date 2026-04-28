---
name: git-workflow
description: "Commit, branch, and PR conventions: small commits with conventional messages, branches named with a type/scope prefix, PRs that fit on one screen and pass CI before requesting review. Includes guidance on when to squash, when to rebase, and how to write a useful PR body."
license: MIT
metadata:
  tags: [git, workflow, github, pr, conventional-commits]
---

# Git workflow

## Branches

- Branch from the current default (usually `main`).
- Name as `<type>/<scope>-<slug>`: `feat/auth-rotate-jwt`, `fix/billing-currency-mismatch`, `chore/bump-deps`.
- One branch = one logical change. If a refactor falls out of a feature, land it on a separate branch first.

## Commits

Conventional commits with a scope:

- `feat(auth): rotate JWT on every refresh`
- `fix(billing): handle GBP correctly when stripe returns lowercase iso codes`
- `chore(deps): bump opennext-cf to 1.6.2`
- `docs(readme): add env var table for the staging cluster`
- `refactor(loader): extract frontmatter parser to its own module`

Keep commits small enough to describe in one sentence. Squash WIP commits before opening a PR.

## PRs

Title = the merged-commit subject (so the merged history reads cleanly).
Body has three sections:

1. **Why** — one paragraph. What problem are we solving?
2. **What changed** — bulleted list, terse.
3. **Test plan** — what you ran locally + what to verify in review.

Keep diffs small enough to review in one sitting. If the diff exceeds ~400 lines, consider splitting unless the change is genuinely atomic (e.g. a generated file).

## Local checks before pushing

- Run `npm run typecheck` (or the language's equivalent).
- Run the test suite — at minimum the files you touched.
- Confirm no debugging artifacts (`console.log`, `dbg!`, commented-out code) slipped in.

## Review etiquette

- Don't request review on a draft.
- Don't force-push after a reviewer has commented unless you announce it ("force-pushed to address feedback").
- Address every comment, even if just to say "deferred — see issue #X".
