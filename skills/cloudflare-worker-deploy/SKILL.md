---
name: cloudflare-worker-deploy
description: Deploy a Cloudflare Worker via wrangler with env-aware secrets. Pick the right wrangler.jsonc env, run wrangler deploy, and verify the deploy with wrangler tail. Covers common errors — missing secret bindings, KV namespace mismatches, route collisions.
license: Apache-2.0
metadata:
  tags: [cloudflare, worker, wrangler, deploy]
---

# Cloudflare Worker deploy

## Pre-flight

1. Confirm `wrangler.jsonc` exists at the repo root. Modern Cloudflare projects use `wrangler.jsonc` (JSON with comments), not `wrangler.toml`. If you see only `wrangler.toml`, ask before migrating.
2. List the configured environments — they're under the `env.*` keys in the JSONC.
3. Verify any secrets required by the env are set: `wrangler secret list --env <env>`.

## Deploy

```bash
wrangler deploy --config wrangler.jsonc --env <env>
```

Watch the output for the deployed route. If you see "binding not found" the secret/KV/D1 binding referenced in JSONC is missing in this environment — set it before re-running.

## Verify

```bash
wrangler tail --env <env>
```

Send a request to the route. If logs don't appear, the route or zone is misconfigured. Don't rely on `curl` against the `workers.dev` subdomain to verify production routes — they're separate from custom-domain routes.

## OpenNext / Next.js sites

If `wrangler.opennext.jsonc` is also present, the deploy command for that surface is:

```bash
wrangler deploy --config wrangler.opennext.jsonc --env <env>
```

Never deploy as Cloudflare Pages — modern Cloudflare Next.js deploys go through OpenNext workers, not Pages.

## Rollback

`wrangler rollback` reverts to the previous deployment. It does **not** roll back secrets or KV/D1 schema — those are out-of-band and need their own rollback plan.
