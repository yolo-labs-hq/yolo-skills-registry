---
name: next-15-app-router
description: Patterns for Next.js 15 App Router projects — server vs. client components, route handlers, dynamic params (now async), revalidation primitives, and metadata. Covers gotchas introduced when upgrading from Next 14, including the params Promise change and async cookies/headers.
license: MIT
metadata:
  tags: [next, nextjs, react, app-router, server-components]
---

# Next.js 15 App Router

## Server vs. client components

- Default to **server components** (no `"use client"` directive). They run on the server, can fetch data directly with `await fetch(...)` or `await db.query(...)`, and don't ship JS to the browser.
- Add `"use client"` only when you need state, effects, or browser APIs (event handlers, `useState`, `useEffect`, `localStorage`, etc.).
- A client component cannot import a server component as a child; pass the server component in via `children` or props instead. This is the "interleaving" pattern.

## Async params (Next 15 change)

In page/layout components, `params` and `searchParams` are now `Promise<...>`:

```tsx
export default async function Page({
  params,
}: {
  params: Promise<{ id: string }>;
}) {
  const { id } = await params;
  // ...
}
```

This breaks the Next 14 pattern where `params` was a plain object. If you're upgrading, every dynamic route needs to be migrated. The codemod `next codemod next-async-request-api .` handles most of it.

## Async cookies/headers

`cookies()`, `headers()`, and `draftMode()` are also async in Next 15:

```tsx
import { cookies } from "next/headers";
const cookieStore = await cookies();
const token = cookieStore.get("auth")?.value;
```

## Route handlers

`app/api/*/route.ts` exports `GET`, `POST`, `PUT`, `DELETE`, etc. They run as server functions. Return a `Response` directly — `NextResponse.json(...)` is a thin helper but plain `Response.json(...)` works just as well in Node 20+.

```ts
export async function GET(request: Request) {
  const { searchParams } = new URL(request.url);
  return Response.json({ id: searchParams.get("id") });
}
```

## Revalidation

- `revalidatePath("/some/route")` — invalidate the route cache.
- `revalidateTag("user-data")` — invalidate fetches tagged with the same string at fetch time (`fetch(url, { next: { tags: ["user-data"] } })`).

Both are server-only — call them from server actions or route handlers, never from a client component.

## Metadata

Use the `metadata` export (static) or `generateMetadata` (dynamic) in `layout.tsx`/`page.tsx`. Don't reach for the legacy `<Head>` component — App Router ignores it.

```tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "Settings",
  description: "Manage your account",
};
```

For dynamic metadata that depends on params:

```tsx
export async function generateMetadata({
  params,
}: {
  params: Promise<{ id: string }>;
}): Promise<Metadata> {
  const { id } = await params;
  const item = await getItem(id);
  return { title: item.name };
}
```

## Edge runtime

Avoid Edge runtime (`export const runtime = "edge"`) unless you have a specific reason — it has a smaller dependency surface (no Node APIs, no large npm packages) and most projects deploy fine on Node runtime.

## OpenNext on Cloudflare

If this project deploys via OpenNext on Cloudflare Workers, the `wrangler.opennext.jsonc` is the source of truth for env vars and bindings. App Router code itself doesn't change — but server-only env access uses `process.env` and is wired up by OpenNext at build time.
