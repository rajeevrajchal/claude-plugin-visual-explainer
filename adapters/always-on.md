# Always-on snippet

The skill normally loads only when the agent decides its description matches. To make it apply every session, paste the block below into your project's `CLAUDE.md` or `AGENTS.md`.

```markdown
## Visual Explainer

- Before creating, modifying or deleting any file, load the `visual-explainer` skill and show its change preview (intent, file table, risk), then stop until I accept. Skip this only when I have already approved that exact change or told you to proceed without asking.
- When an answer is about how things connect or what happens in what order, load the `visual-explainer` skill and reply with one small diagram plus brief text.
- Diagram format: ASCII in the terminal, Mermaid in Markdown files.
```

Change the last line to `always use Mermaid` or `ASCII only` to fix the format permanently.
