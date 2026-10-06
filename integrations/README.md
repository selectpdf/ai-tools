# Integrations — Cursor, GitHub Copilot, Codex

The SelectPdf knowledge in this repo is authored as **Claude Code Agent Skills** under
[`plugins/selectpdf/skills/`](../plugins/selectpdf/skills/). Those skills' `reference/*.md` files are the
**single source of truth** (plain, tool-agnostic Markdown, every sample in C# and VB.NET).

This folder adapts that knowledge for other AI coding assistants. Each file is a **condensed,
self-contained** guide (install, quickstart, mental model, engines, gotchas, a short cheat sheet) that
**links to the full reference files on GitHub** for depth — so there is one place to maintain the deep
content.

Four guides, mirrored across every tool:

| File stem | Library | Package |
|---|---|---|
| `selectpdf-lib-universal-net` | SelectPdf.Universal, cross-platform commercial (Windows, Linux, macOS on Apple Silicon, Docker) | `SelectPdf.Universal` + `SelectPdf.Universal.Native.<rid>` |
| `selectpdf-lib-htmltopdf-universal-net` | free cross-platform Community Edition (HTML→PDF, 5-page cap) | `SelectPdf.HtmlToPdf.Universal` + `SelectPdf.Universal.Native.<rid>` |
| `selectpdf-lib-net` | classic commercial Select.Pdf (Windows) | `Select.Pdf` / `Select.Pdf.NetCore` |
| `selectpdf-lib-htmltopdf-net` | classic free Community Edition (Windows, HTML→PDF, 5-page cap) | `Select.HtmlToPdf` / `Select.HtmlToPdf.NetCore` |

Install only the guide(s) for the package your project references; each one names the right sibling if
the project turns out to use another edition.

## Cursor — `cursor/*.mdc`

Cursor [project rules](https://docs.cursor.com/context/rules). Copy the `.mdc` file(s) into your
project's `.cursor/rules/` folder:

```text
your-project/.cursor/rules/selectpdf-lib-net.mdc
```

They are authored as **Agent Requested** rules (a `description`, no `globs`, `alwaysApply: false`), so
Cursor pulls them in when your request is about SelectPdf. To always apply, set `alwaysApply: true`; to
auto-attach on .NET files, set `globs: **/*.cs,**/*.vb` (noisier).

## GitHub Copilot — `copilot/*.instructions.md`

Copilot [custom instructions](https://docs.github.com/en/copilot/customizing-copilot/adding-repository-custom-instructions-for-github-copilot).
Copy into your project's `.github/instructions/`:

```text
your-project/.github/instructions/selectpdf-lib-net.instructions.md
```

The `applyTo` frontmatter defaults to `**/*.cs,**/*.vb`. Narrow it to where you actually do PDF work
(e.g. `applyTo: "src/Pdf/**"`) to keep it out of unrelated context. Requires the instructions-files
setting enabled in your Copilot client.

## Codex — `codex/*.md`

OpenAI Codex reads [`AGENTS.md`](https://agents.md). Either paste the relevant file's contents into
your project's root `AGENTS.md`, or keep the file in your repo and add one line to `AGENTS.md`:

```markdown
For SelectPdf .NET work, follow ./docs/selectpdf-lib-net.md
```

## Keeping these in sync

When the SDK changes, update the `reference/*.md` files under `plugins/selectpdf/skills/` first, then
refresh the condensed sections here if a quickstart, option, or gotcha changed. The reference links
below always point at the canonical content, so depth never goes stale.
