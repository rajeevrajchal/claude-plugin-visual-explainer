# Architecture graph

Answers: **what are the parts, and what talks to what?**

Use for: how frontend/API/services/storage fit together, where a new component sits, the boundaries of a feature.

## Rules

- Direction: `TD` for layered systems (UI on top, storage at the bottom), `LR` for pipelines.
- Group by boundary (client / server / external) with `subgraph` only when there are 2+ boundaries. Don't nest subgraphs.
- Edges point in the direction of the call or request. Label them only when the verb matters.
- External services (Supabase, Stripe, S3) are their own nodes, labelled as external.
- If the user's request adds something, mark new nodes `NEW` and new edges dashed.

## Mermaid

```mermaid
flowchart TD
    subgraph client["Client"]
        login["Login page"]
    end
    subgraph server["Server"]
        api["API routes"]
        mw["Auth middleware"]
        authSvc["Auth service"]
    end
    supa[("Supabase Auth (external)")]
    db[("Postgres")]

    login -->|credentials| authSvc
    authSvc --> supa
    login --> api
    api --> mw
    mw -->|verify token| authSvc
    api --> db
```

With real files (codebase available):

```mermaid
flowchart LR
    loginUi["routes/login/+page.svelte"]
    authSvc["lib/auth/service.ts"]
    store["lib/auth/store.ts"]
    client["lib/api/client.ts"]

    loginUi --> authSvc
    authSvc --> client
    authSvc --> store
```

## ASCII

```
 Client                 Server                         External
 ──────                 ──────                         ────────
 Login page ──creds──▶  Auth service ───────────────▶ Supabase Auth
     │                       ▲
     ▼                       │ verify token
 API routes ──▶ Auth middleware
     │
     ▼
 Postgres
```

Keep ASCII boxes optional. Plain labels plus `──▶ │ ▼ ▲` arrows read faster than drawn boxes.
