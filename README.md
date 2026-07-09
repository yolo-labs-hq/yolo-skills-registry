# yolo-skills-registry

Curated registry of agent skills for yolo-code - playbook-style guidance that ships as `SKILL.md` files (the [agentskills](https://github.com/agentskills/agentskills) spec).

## Install a skill

```bash
yolo skills install <name>
```

This fetches the registry index, downloads the matching subdirectory from this repo, and copies it to `~/.config/yolo-code/skills/<name>/`. Once installed, the skill's catalog entry shows up in any yolo-code session.

To install a skill from a custom location instead of this registry, point at a tarball URL:

```bash
yolo skills install https://example.com/my-skill.tar.gz
```

Or override the registry URL globally:

```bash
export YOLO_SKILLS_REGISTRY_URL=https://my-fork/registry.json
export YOLO_SKILLS_REGISTRY_TARBALL_URL=https://my-fork/archive/main.tar.gz
```

## Available skills

| Name | Tags | What it covers |
|------|------|---------------|
| `cloudflare-worker-deploy` | cloudflare, worker, wrangler | Deploy a Cloudflare Worker via wrangler.jsonc, env-aware secrets, OpenNext, rollback. |
| `git-workflow` | git, github, pr | Conventional commits, branch naming, small PRs, review etiquette. |
| `vitest-runner` | vitest, test, tdd | `vitest run` over watch, basic reporter, first-failure-first triage. |
| `mcp-server-debug` | mcp, debug, stdio | Validate the MCP handshake, inspect tools/list, env-var injection, schema mismatches. |
| `next-15-app-router` | next, nextjs, react | Server vs. client components, async params (Next 15 change), revalidation, metadata. |
| `xquik-social-data` | xquik, x-twitter, social-media, mcp, automation | Use Xquik for X/Twitter research, analytics, monitoring, and approval-gated publishing workflows. |

## Repo layout

```
yolo-skills-registry/
├── README.md
├── registry.json            # canonical index (consumed by `yolo skills install`)
└── skills/
    ├── cloudflare-worker-deploy/
    │   └── SKILL.md
    ├── git-workflow/
    │   └── SKILL.md
    └── …
```

## `registry.json` schema

```json
{
  "version": 1,
  "publishedAt": "2026-04-28T00:00:00Z",
  "skills": {
    "<skill-name>": {
      "description": "<short summary, ≤200 chars>",
      "path": "skills/<skill-name>",
      "tags": ["tag1", "tag2"],
      "endorsed": true,
      "version": "1.0.0",
      "license": "MIT"
    }
  }
}
```

`path` is the directory inside this repo that contains the `SKILL.md`. `endorsed` marks skills that are officially curated by the YOLO Studio team (vs. community submissions, once those land).

## Contributing a skill

1. Fork this repo.
2. Add a directory under `skills/<your-skill-name>/` with a single `SKILL.md`. The frontmatter must include `name` (kebab-case, matching the dir) and `description` (≤1024 chars). See the [agentskills spec](https://github.com/agentskills/agentskills/blob/main/docs/specification.mdx) for the full schema.
3. Validate locally: `yolo skills validate skills/<your-skill-name>`.
4. Add a corresponding entry to `registry.json`.
5. Open a PR. Keep skills focused — one playbook per skill.

Before opening a PR, also check that the registry entry points at a real
`SKILL.md` file:

```bash
node -e 'const fs=require("fs");const r=JSON.parse(fs.readFileSync("registry.json","utf8"));for(const [name,s] of Object.entries(r.skills)){const p=`${s.path}/SKILL.md`;if(!fs.existsSync(p)) throw new Error(`${name} missing ${p}`)}'
```

## License

Each `SKILL.md` declares its own license in the frontmatter. The registry index (this repo's structure and `registry.json`) is MIT.
