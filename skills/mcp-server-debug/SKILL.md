---
name: mcp-server-debug
description: Diagnose an MCP server that is failing to register tools or returning errors. Walks through stdio handshake validation, env-var injection, tool-schema introspection, and host-side debugging so you can localize whether the bug is in the server, the transport, or the host's tool routing.
license: MIT
metadata:
  tags: [mcp, model-context-protocol, debug, stdio]
---

# MCP server debug

## 1. Validate the handshake

Spawn the server manually and feed it the `initialize` request:

```bash
echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{}}}' | <your-server-command>
```

A working server responds within ~1s with `{"result":{"protocolVersion":...,"capabilities":...}}`.

- No response → the server is failing at startup. Check stderr.
- Garbled response → the server is writing logs to stdout instead of stderr. Logs **must** go to stderr; stdout is reserved for JSON-RPC frames.
- Wrong protocol version → the host you're targeting may speak an older version. Match it.

## 2. List tools

```bash
echo '{"jsonrpc":"2.0","id":2,"method":"tools/list"}' | <server-command>
```

Empty `tools` array = the server initialized but registers no tools. Walk its registration logic — most often a missing `await` around the registration call, or a try/catch that swallows the error.

## 3. Env-var injection

MCP servers run as subprocesses spawned by the host. If the server depends on a token (e.g. `MY_API_KEY`), the host must inject it via the `env` field in the server config. Sanity check at server startup:

```ts
// At the very top of your server's entrypoint:
console.error("env keys:", Object.keys(process.env).filter(k => k.startsWith("MY_")));
```

This goes to stderr (which the host captures) without polluting the JSON-RPC stream.

## 4. Tool-call schema mismatches

If the host accepts the server but tool calls fail, inspect the host's request and compare against the server's declared schema (`inputSchema`). Mismatched required fields are the most common bug. The host will typically include the offending args in its error message — read them carefully.

## 5. Host-side debug

If the server passes 1–4 in isolation but the host doesn't see it:

- The host's MCP client config may be cached. Restart the host.
- The server's stdout may include a UTF-8 BOM or non-JSON prelude. Pipe through `head -c 200 | xxd` to spot.
- Pipe-buffering issues: ensure the server flushes stdout after every frame (Node does this by default; Python's `print` does not unless you pass `flush=True` or unbuffered mode).

## 6. Streamable HTTP transport

For HTTP transport (not stdio), validate the endpoint with `curl` first:

```bash
curl -X POST <url> -H 'Content-Type: application/json' \
  -d '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2025-06-18","capabilities":{}}}'
```

A 4xx means auth is wrong; a 5xx means the server crashed; a 200 with no `result` means the handler returned the wrong shape.
