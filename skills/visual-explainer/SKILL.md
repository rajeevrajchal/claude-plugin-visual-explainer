---
name: visual-explainer
description: Load before replying whenever the reply will explain or propose something with structure - how parts of a system interact, a multi-step implementation plan, a refactor, migration or new layer you are describing or about to make, what happens during a request or login, states or lifecycles, module dependencies, how a repo is organized, a summary of a long plan, or a change across many files - or when asked to visualize, diagram or map something. Also load before creating, modifying or deleting files, to show a change preview (which files, what code changes) and wait for approval. Gives one small Mermaid or ASCII diagram or table plus brief text instead of long prose. Not for simple factual or single-step questions.
license: MIT
metadata:
  version: "0.1.0"
---

# Visual Explainer

Make complex technical answers easier to take in by turning the *relationships* in them into one small diagram. The goal is less reading, not more decoration.

## 1. Gate: should this be visual at all?

Draw a diagram only if at least one of these holds:

- 3+ components, files, services or modules **and** the answer depends on how they connect
- 3+ steps where order, branching, dependencies or parallel work matter
- 2+ actors exchanging messages in a specific order (request/response, auth, webhooks)
- A thing that moves between 3+ named states
- A structural change (refactor, migration, redesign): before vs after
- You are about to create, modify or delete files (any number): change preview, see section 5
- 4+ files already created, modified or deleted in one change

Otherwise answer in plain text. Never add a diagram to a short factual answer, a single command or a yes/no question. A one-file fix gets the change preview table and no diagram. A list of steps with no branching or dependencies is a numbered list, not a flowchart.

If you were invoked explicitly (a command or the user asked for a diagram), skip the gate. If there is genuinely nothing relational to draw, say so in one line and answer in text.

## 2. Pick one diagram type

| The content is mainly...                               | Use                        | Reference |
|--------------------------------------------------------|----------------------------|-----------|
| Components/services and what talks to what            | Architecture graph         | [architecture.md](references/architecture.md) |
| Which file/module/package imports or calls which       | Dependency graph           | [dependencies.md](references/dependencies.md) |
| Ordered steps, plans with task dependencies, user journeys, progress | Flowchart     | [flow.md](references/flow.md) |
| Actors exchanging messages over time                   | Sequence diagram           | [sequence.md](references/sequence.md) |
| An entity's lifecycle / modes                          | State diagram              | [state.md](references/state.md) |
| Repo layout, or what a change touched                  | Tree                       | [structure.md](references/structure.md) |
| A change you are about to make to files                | Change preview (table)     | [change-preview.md](references/change-preview.md) |
| A system before and after a change                     | Before/after pair          | [before-after.md](references/before-after.md) |
| Data model, class hierarchy, timeline, idea map        | ER / class / timeline / mindmap | [other.md](references/other.md) |

Tie-breakers:
- "How do A, B and C interact / fit together / work together" with no specific scenario → **architecture**. Use a sequence diagram only for a named scenario ("when the user logs in", "during checkout", "on each request").
- "Plan the steps" / "how would you add X" → **flow** (task dependencies), even if no code is written yet.
- "Refactor / migrate / introduce a layer / replace X" → **before/after**, even when you only describe the change.

Read only the reference for the type you chose. Default to **one** diagram. A before/after pair counts as one. Use two only when they answer different questions (e.g. architecture + the order of a login request), never to show the same thing twice.

## 3. Pick the format: will Mermaid actually render?

Mermaid is the best format when the reader sees it rendered, and the worst when they see raw source. Decide per response:

- **Mermaid** when the output lands somewhere that renders it: a Markdown file you are writing (docs, ADRs, READMEs, PR or issue descriptions), GitHub/GitLab, an IDE Markdown preview, or a chat UI the user has said renders Mermaid. Also whenever the user asks for Mermaid.
- **ASCII/Unicode** when replying inline in a terminal agent (CLI/TUI), or when you don't know whether Mermaid renders. ASCII is readable everywhere.
- When you write ASCII inline and the diagram is worth keeping, you may add one line offering the Mermaid version, or, if you are already writing a docs file, put the Mermaid there.

The user's explicit preference ("always use Mermaid", "ASCII only") overrides this. Each reference file shows both forms for its type.

ASCII must be unambiguous. Draw arrows only for chains and simple trees. If a graph has more than about 6 nodes, or any edge would cross another or need a long detour, write it as an **adjacency list** (`a.ts → b.ts, c.ts`), one source per line. A neat list is better than a drawing where an arrow could be read as the wrong edge.

## 4. Ground it in the real project

- If you can read the repository, use real names for nodes: `auth/service.ts`, `LoginForm.svelte`, `users` table. Look at the code (imports, routes, calls, schema) before drawing dependency or structure diagrams. Don't draw them from memory.
- Draw only relationships that the code, the user, or your own stated plan supports. Do not invent edges to make a diagram look complete.
- Show proposed or uncertain things differently from existing ones: dashed edges (`-.->` / `- - >`) labelled `planned` or `?`, and one line saying what is unverified.
- Use conceptual names (Auth Service, Database) only when there is no codebase or the user is asking conceptually.

## 5. Change preview: before you edit

Before creating, modifying or deleting files, reply with the change preview from [change-preview.md](references/change-preview.md): the intent in one sentence, a table of file / action / what changes (one row per file; action is `NEW`, `CHANGED`, `REMOVED` or `MOVED`), a flow line if the files relate, one risk line, then `Proceed?`. Then stop. Do not edit until the user accepts.

- This applies to any number of files, including one.
- In plan mode, the preview is the plan you present for approval.
- Skip it only when the user has already approved this exact change or told you to proceed without asking.
- After acceptance, make exactly the listed changes. If the scope grows, show the new rows and ask again.

## 6. Shape of the response

```
<1-2 sentence summary: the point of the diagram>

<the diagram>

<only what the diagram can't show: key decisions, risks, constraints>

<files / tasks: numbered list, if the user has to act on it>
```

- The diagram replaces explanation. Do not walk through it edge by edge afterwards.
- Keep it small: about 12 nodes or fewer, 15 at most. If it's bigger, group nodes (subgraphs, or "3 route files" as one node) or zoom into the part that matters and say what you left out.
- Short labels (1–4 words or a file path). Put detail in the text, not in the boxes.
- One direction of flow (top→down or left→right), no crossing spaghetti. Edge labels only where the verb matters (`validates`, `reads`, `emits`).
- For a long plan or a long agent output, compress it to: Summary → Visual → Important details → Files/Tasks. Your reply must be clearly **shorter than the input** (aim for half or less). Keep the risks the original names. Add your own critique only for a real defect, as at most 2 one-line bullets. Do not restate the original.

## 7. Mermaid safety rules (avoid render errors)

- Node IDs: plain alphanumerics (`authSvc`, `db1`). Never use `end`, `graph`, `subgraph`, `class` or `style` as IDs.
- Put any label containing spaces, slashes, dots, parentheses, brackets, colons or quotes in double quotes: `authSvc["auth/service.ts"]`. Use `#quot;` for a literal double quote inside a label.
- Use `flowchart` (not legacy `graph`) for flows and architecture, plus `sequenceDiagram`, `stateDiagram-v2`, `erDiagram`, `classDiagram`, `timeline`, `mindmap`.
- No HTML, no styling/themes, no `click` handlers. Colour must never carry meaning; use labels like `NEW`, `CHANGED`, `REMOVED`.
- One diagram per fenced ```mermaid block.

## 8. Only visualize what you'd say out loud

Diagram only information that is appropriate to show the user: explicit plans, architecture, implementation steps, tool actions you took, file changes, dependencies, test results, decisions you state. Never diagram private reasoning, hidden deliberation or internal thought process, and don't present one as a "thinking map".

For progress, use only the status your own task list or tool results actually report: `✓` done, `●` in progress, `○` not started. If you have no reliable status, don't draw progress.

## 9. Explicit invocation

When invoked with arguments, the first word may name a type: `architecture`, `dependencies`, `flow`, `sequence`, `state`, `structure`, `changes`, `preview`, `before-after`, or `auto`. Everything after it is the subject. With no type, or `auto`, pick the type yourself using section 2. With no subject, use the most recent plan, explanation or change in the conversation. If there is none, use the current repository.
