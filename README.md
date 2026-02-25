# pi-critique

Structured AI critique for writing and code in [pi](https://github.com/badlogic/pi-mono). Submits a critique prompt to the model, which returns numbered critiques (C1, C2, ...) with inline markers in the original document. Pairs well with [pi-annotated-reply](https://github.com/omaclaren/pi-annotated-reply) and [pi-markdown-preview](https://github.com/omaclaren/pi-markdown-preview) but works standalone.

## Commands

| Command | Description |
|---------|-------------|
| `/critique` | Critique the last assistant response |
| `/critique <path>` | Critique a file |
| `/critique --code` | Force code review lens |
| `/critique --writing` | Force writing critique lens |
| `/critique --no-inline` | Critiques list only, no annotated document |
| `/critique --edit` | Load prompt into editor instead of auto-submitting |
| `/critique --help` | Show usage |

## How it works

`/critique` sends a structured prompt to the model asking it to:

1. Assess the document overall
2. Produce numbered critiques (C1, C2, ...) with type, severity, and exact quoted passage
3. Reproduce the document with `{C1}`, `{C2}` markers at each critiqued location

The model adapts critique types to the genre:

- **Expository/technical**: overstatement, credibility, evidence, wordiness, factcheck, ...
- **Creative/narrative**: pacing, voice, tension, clarity, ...
- **Academic**: methodology, citation, logic, scope, ...
- **Code**: bug, performance, readability, architecture, security, ...

Types are not fixed — the model chooses what fits the content.

## Lenses

The extension auto-detects whether content is code or writing based on file extension. Override with `--code` or `--writing`.

Code files (`.ts`, `.py`, `.rs`, `.go`, etc.) get a code review prompt. Writing files (`.md`, `.txt`, `.tex`, etc.) get a writing critique prompt. Extensionless files like `Dockerfile` and `Makefile` are detected as code.

## Large files

Files over 500 lines are handled differently to save tokens: the extension passes the file path to the model (rather than embedding the content), and the model reads the file with its tools and writes an annotated copy to `<filename>.critique.<ext>` on disk.

## Example output

```markdown
## Assessment

Strong opening, but several unsupported claims weaken credibility...

## Critiques

**C1** (overstatement, high): *"Every study shows that context-switching destroys productivity"*
"Every study" is a universal claim that's easy to falsify. Consider: "Research consistently shows..."

**C2** (credibility, medium): *"No benchmark data is available yet, but informal testing confirms..."*
This sentence contradicts itself. Either provide numbers or drop the comparison.

## Document

Original text with markers showing where each critique applies.
Some text here. {C1} More text. {C2}
```

## Reply loop

After receiving a critique, respond with bracketed annotations:

```
[accept C1]
[reject C2: the simplicity claim is intentional]
[revise C3: good point, will soften]
[question C4: can you elaborate?]
```

This works standalone in pi's editor, or with `/reply` from [pi-annotated-reply](https://github.com/omaclaren/pi-annotated-reply) for a smoother workflow.

## Install

```bash
pi install npm:pi-critique
```

Or from GitHub:

```bash
pi install https://github.com/omaclaren/pi-critique
```

Or try it without installing:

```bash
pi -e https://github.com/omaclaren/pi-critique
```

## License

MIT
