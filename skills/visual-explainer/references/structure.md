# Trees: repository structure and change overviews

Trees are plain text in every host, so you don't need Mermaid here.

## Repository structure

Answers: **where do things live?**

- List the real directories (use your file tools). Don't reconstruct the tree from memory.
- Show 2 levels by default, and go deeper only along the path that matters for the question.
- Skip generated and vendored dirs (`node_modules`, `dist`, `.git`, build output).
- Add a short role comment for each important entry. Leave out trivial files.

```
src/
├── routes/          # SvelteKit pages + API endpoints
│   ├── login/
│   └── api/
├── lib/
│   ├── auth/        # session + Supabase wrapper
│   ├── api/         # fetch client used by routes
│   └── stores/      # UI state
└── components/      # shared UI, no data access
```

Follow it with 2–4 lines on how the parts relate. If those relationships are the point, add an architecture graph instead of more prose.

## File-change overview

Use after changing 4+ files. Group by status. Paths only, no code. Before changing files, use [change-preview.md](change-preview.md) instead.

```
Authentication change
├── NEW
│   ├── src/lib/auth/service.ts
│   └── src/lib/auth/types.ts
├── CHANGED
│   ├── src/routes/login/+page.svelte
│   └── src/lib/api/client.ts
└── REMOVED
    └── src/lib/old-auth.ts
```

Use text labels (NEW / CHANGED / REMOVED) rather than colour or emoji alone, because they survive every terminal and screen reader. Add the 🟢🟡🔴 dots only if the user asks for them. Only list files you actually changed.
