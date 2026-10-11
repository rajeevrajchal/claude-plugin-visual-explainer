# Change preview

Answers: **what are you about to change, and where?**

Use before creating, modifying or deleting files, for any number of files. The reader should be able to accept or reject the change from this alone.

## Rules

- First line is the intent: one sentence, what the change achieves.
- One table row per file. Action is `NEW`, `CHANGED`, `REMOVED` or `MOVED`.
- "What changes" names the real symbols and behaviour: functions added or removed, signatures changed, what a call now goes through. Not "update logic".
- List only files you have read (or will create). Mark anything unverified with `?`.
- Add a flow line only when the touched files relate to each other. For 6+ related files use an adjacency list.
- Show code only for the one change that is risky or not obvious from the table, as a diff of 10 lines or fewer. Otherwise no code.
- One `Risk:` line. Write `Risk: none known` rather than inventing one.
- End with `Proceed?` and stop. Do not edit until the user accepts.
- After acceptance, make exactly the listed changes. If the scope grows, show the new or changed rows and ask again.

## Template

```
Intent: move SQL out of the login handler into a repository.

| File                      | Action  | What changes                             |
|---------------------------|---------|------------------------------------------|
| src/lib/auth/repo.ts      | NEW     | findUserByEmail(), upsertProfile()       |
| src/routes/api/login.ts   | CHANGED | inline SQL → calls repo; POST unchanged  |
| src/lib/old-auth.ts       | REMOVED | unused after this change                 |

Flow: login.ts ──▶ repo.ts ──▶ Postgres
Risk: upsertProfile() now runs outside the handler's transaction.

Proceed?
```

With the one risky change shown:

```diff
- const user = await sql`select * from users where email = ${email}`
+ const user = await repo.findUserByEmail(email)
```

## One-file change

Same shape, one row, no flow line:

```
Intent: stop the token refresh loop on 401.

| File                   | Action  | What changes                                |
|------------------------|---------|---------------------------------------------|
| src/lib/api/client.ts  | CHANGED | refresh() retries once, then throws AuthError |

Risk: callers that swallowed the old silent retry will now see the error.

Proceed?
```
