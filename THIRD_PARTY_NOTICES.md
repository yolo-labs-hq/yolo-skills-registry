# Third-Party Notices

This registry includes skills lifted from third-party repositories. Each skill
directory carries the upstream `LICENSE` file alongside the lifted
`SKILL.md`. Source URLs are also stamped into each skill's frontmatter under
`metadata.source` and into `registry.json` under the per-skill `source` field.

## anthropics/skills (Apache License 2.0)

- `skills/frontend-design/` — lifted from
  https://github.com/anthropics/skills/tree/main/skills/frontend-design

The Apache 2.0 license text is preserved at `skills/frontend-design/LICENSE`.

## addyosmani/agent-skills (MIT)

- `skills/code-review-and-quality/`
- `skills/debugging-and-error-recovery/`
- `skills/code-simplification/`
- `skills/test-driven-development/`

All four are lifted from
https://github.com/addyosmani/agent-skills/tree/main/skills/<name>. The MIT
license text (Copyright (c) 2025 Addy Osmani) is preserved at
`skills/<name>/LICENSE` for each.

## First-party skills

The remaining skills (`cloudflare-worker-deploy`, `git-workflow`,
`vitest-runner`, `mcp-server-debug`, `next-15-app-router`) are authored by the
yolo-labs-hq team and licensed under the terms in each skill's frontmatter.
