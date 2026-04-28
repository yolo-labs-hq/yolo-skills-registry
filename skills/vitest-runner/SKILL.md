---
name: vitest-runner
description: |
  Run vitest with the project's own config. Prefer `vitest run path/to/file.test.ts` over watch mode during agent sessions to avoid leaving processes behind. Use `--reporter=basic` for compact output the agent can parse, and `--coverage` only when explicitly asked.
license: MIT
compatibility: vitest >=1.0
metadata:
  tags: [vitest, test, javascript, typescript, tdd]
---

# Vitest runner

## Targeted runs

```bash
vitest run --reporter=basic path/to/file.test.ts
```

Always prefer this over `vitest` (watch mode) — agent sessions should not leave a long-running process behind. If you do want watch mode for a human session, `vitest watch` is explicit and easier to spot in `ps`.

## Multiple files

```bash
vitest run --reporter=basic 'src/**/*.test.ts'
```

Quote the glob — your shell may expand it differently than vitest expects.

## Filtering by test name

```bash
vitest run --reporter=basic -t "describe-name pattern"
```

## When tests fail

1. Read the **first** failure carefully. vitest reports failures in source order, and one root cause often cascades into several apparent failures further down. Fixing the first usually clears the rest.
2. Re-run the failing file with `--reporter=verbose` for stack details:
   ```bash
   vitest run --reporter=verbose path/to/file.test.ts
   ```
3. Don't stub or mock to make a test pass without understanding the underlying break. If the production code is wrong, fix the production code; if the test is wrong, justify the change in the commit message.

## Coverage

Only when explicitly requested:

```bash
vitest run --coverage --reporter=basic
```

`--coverage` slows the run substantially and writes a `coverage/` directory. Don't enable it by default.

## Snapshot tests

`vitest run -u` updates snapshots. Only do this when the snapshot output is reviewed and intentional — never as a way to make a snapshot test pass.
