<p align="center">
  <a href="https://selectpdf.com">
    <img src="images/logos/selectpdf-logo-h128-amberbg.png" alt="SelectPdf" height="96">
  </a>
</p>

<h1 align="center">SelectPdf — AI Tools</h1>

<p align="center">
  Skills that teach AI coding assistants (Claude Code, and any agent that reads
  <a href="https://docs.claude.com/en/docs/claude-code/skills">Agent Skills</a>) how to use
  <a href="https://selectpdf.com">SelectPdf</a> correctly — accurate APIs, real code, both C# and VB.NET.
</p>

---

## What's in here

This repository is the home for all SelectPdf AI tooling. It is packaged as a single Claude Code
plugin, **`selectpdf`**, exposed through a plugin marketplace.

### Plugin: `selectpdf`

| Skill | Covers | Package |
|---|---|---|
| **`selectpdf-lib-net`** | The full **commercial** Select.Pdf .NET library — HTML→PDF, build/edit PDFs, merge/split, AcroForms, digital signatures, encryption, PDF→text/image, accessible tagged PDF/UA-1 and PDF/A-3. | [`Select.Pdf`](https://www.nuget.org/packages/Select.Pdf/) / [`Select.Pdf.NetCore`](https://www.nuget.org/packages/Select.Pdf.NetCore/) |
| **`selectpdf-lib-htmltopdf-net`** | The free **Community Edition** — HTML→PDF only, 5 pages per document. | [`Select.HtmlToPdf`](https://www.nuget.org/packages/Select.HtmlToPdf/) / [`Select.HtmlToPdf.NetCore`](https://www.nuget.org/packages/Select.HtmlToPdf.NetCore/) |

Every skill ships a lean `SKILL.md` (quickstart, decision routing, gotchas) plus topic-by-topic
reference files that load on demand. All code samples are provided in **C# and VB.NET**.

> **Naming.** Library skills are suffixed `-net` because the SelectPdf .NET library is .NET-only.
> Skills for the SelectPdf **online REST API** (which has clients in several languages) will live in
> this same plugin under the `selectpdf-api-*` family — e.g. `selectpdf-api-net`, `selectpdf-api-php`.

## Install (Claude Code)

```text
/plugin marketplace add selectpdf/ai-tools
/plugin install selectpdf@selectpdf
```

Then the right skill activates automatically when you ask Claude to generate or manipulate PDFs in a
.NET project. You can also copy any `skills/<name>/` folder into your project's `.claude/skills/`
directory to use it without the plugin.

## Versioning

Content tracks **SelectPdf v26.3** ("2026 Vol 3"). API signatures and feature availability are taken
verbatim from the shipped SDK reference and official samples.

## Resources

- 📖 Library docs — https://selectpdf.com/pdf-library/
- 🧪 Live demo (C# / VB.NET) — https://selectpdf.com/demo/
- ⬇️ Downloads & free trial — https://selectpdf.com/downloads/
- 📦 Library source & README — https://github.com/selectpdf/pdf-library-for-net

---

<p align="center">© SelectPdf — <a href="https://selectpdf.com">selectpdf.com</a></p>
