---
name: xquik-social-data
description: Use Xquik as an API or remote MCP source for X/Twitter search, analytics, monitoring, webhooks, and approval-gated publishing workflows.
license: MIT
metadata:
  tags: [xquik, x-twitter, social-media, mcp, automation]
---

# Xquik social data

Use this skill when a yolo-code session needs public X/Twitter context, social
analytics, monitored account updates, webhook evidence, or approval-gated X
publishing through Xquik.

## 1. Configure Access

Store the Xquik API key in the host environment:

```bash
export XQUIK_API_KEY=your_xquik_api_key
```

For a remote MCP client, use the hosted endpoint:

```json
{
  "servers": {
    "xquik": {
      "type": "http",
      "url": "https://xquik.com/mcp",
      "headers": {
        "Authorization": "Bearer ${XQUIK_API_KEY}"
      }
    }
  }
}
```

## 2. Gather Evidence

Ask the user which account, keyword, monitor, or workflow they want to inspect.
Read through Xquik, then keep the source data attached to the working notes.

Capture:

- source query or account
- timestamp of the read
- tweet or user identifiers when present
- filters, limits, and missing fields

## 3. Draft Separately From Actions

Use Xquik data as evidence for drafts, summaries, rankings, or reports. Keep
publish, reply, follow, and scheduling actions separate from analysis.

Before any write-like action:

1. Show the proposed action and target account.
2. Ask for explicit approval.
3. Record the approved text or operation.
4. Stop if the user changes scope or credentials are missing.

## 4. Safety Checks

- Never print or commit `XQUIK_API_KEY`.
- Do not make private-account claims from public results.
- Do not publish, reply, follow, or message without explicit approval.
- Prefer concise evidence tables over unsupported social-performance claims.
