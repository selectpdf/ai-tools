---
name: selectpdf-lib-htmltopdf-net
description: >-
  Convert HTML and URLs to PDF in .NET (C# / VB.NET) with the free Select.HtmlToPdf Community Edition
  (Select.HtmlToPdf or Select.HtmlToPdf.NetCore NuGet packages). Use for HTML-to-PDF conversion with
  page setup, headers/footers, page numbering, links, bookmarks, web authentication, cookies, HTTP
  headers and proxy. Trigger when a .NET app needs free HTML-to-PDF with SelectPdf. The Community
  Edition is HTML→PDF only and capped at 5 pages per document — for PDF creation/editing, forms,
  signatures or larger output use the commercial selectpdf-lib-net skill; for the online REST API use
  a selectpdf-api-* skill.
---

# SelectPdf HTML-to-PDF — Community Edition (free)

Expert guidance for the **free Select.HtmlToPdf Community Edition** — the same rendering engine as the
commercial library, restricted to **HTML→PDF conversion** and **5 pages per document**. Current as of
**v26.3**.

## Pick the right skill first

- **Community Edition (this skill)** — `Select.HtmlToPdf` / `Select.HtmlToPdf.NetCore`. Free,
  HTML→PDF only, **max 5 pages** per document.
- **Commercial library** — if the project uses `Select.Pdf` / `Select.Pdf.NetCore`, or needs PDF
  creation, editing, merge/split, forms, signatures, encryption, extraction, accessible/PDF-A output,
  or documents longer than 5 pages, use the **`selectpdf-lib-net`** skill instead.
- **Online REST API** — if the project calls `api.selectpdf.com` over HTTP, use a `selectpdf-api-*`
  skill.

Check the project's `.csproj`/`packages.config`: `Select.HtmlToPdf*` = this skill;
`Select.Pdf*` = the commercial skill.

## Install

Match the runtime **and CPU architecture** (x86/x64 mismatch causes `BadImageFormatException`):

**.NET (Core / 5–10 / Standard 2.0):**

```powershell
Install-Package Select.HtmlToPdf.NetCore
```

**.NET Framework:**

```powershell
Install-Package Select.HtmlToPdf
```

**Optional engine runtimes** — only if you select the Blink or Chromium engine:

```powershell
Install-Package Select.HtmlToPdf.NetCore.Blink
Install-Package Select.HtmlToPdf.NetCore.Chromium.Windows
```

> **Platform:** Windows x86/x64 only (native engine dependencies); no Linux/macOS build. The Blink
> engine requires .NET Framework 4.6.1+ (or .NET Core / 5–10).

## The 5-page limit

The Community Edition produces at most **5 pages per document**. Content beyond page 5 is not
rendered. If you need more, upgrade to the commercial `Select.Pdf` library (the API is the same — see
`selectpdf-lib-net`). The CE is free for commercial use within this limit.

## Quickstart

### Convert a URL to PDF

```csharp
using SelectPdf;

HtmlToPdf converter = new HtmlToPdf();
PdfDocument doc = converter.ConvertUrl("https://selectpdf.com");
doc.Save("output.pdf");
doc.Close();   // always close to release resources
```
```vbnet
Imports SelectPdf

Dim converter As New HtmlToPdf()
Dim doc As PdfDocument = converter.ConvertUrl("https://selectpdf.com")
doc.Save("output.pdf")
doc.Close()   ' always close to release resources
```

### Convert an HTML string to PDF

```csharp
using SelectPdf;

HtmlToPdf converter = new HtmlToPdf();
string html = "<h1>Hello, PDF!</h1><p>Generated with Select.HtmlToPdf.</p>";
PdfDocument doc = converter.ConvertHtmlString(html);   // pass a base URL as 2nd arg to resolve relative links/images
doc.Save("hello.pdf");
doc.Close();
```
```vbnet
Imports SelectPdf

Dim converter As New HtmlToPdf()
Dim html As String = "<h1>Hello, PDF!</h1><p>Generated with Select.HtmlToPdf.</p>"
Dim doc As PdfDocument = converter.ConvertHtmlString(html)   ' pass a base URL as 2nd arg to resolve relative links/images
doc.Save("hello.pdf")
doc.Close()
```

## Mental model

- One class does the work: **`HtmlToPdf`**. Configure it through `converter.Options` (an
  `HtmlToPdfOptions`), then call `ConvertUrl(...)` or `ConvertHtmlString(...)`, which returns a
  `PdfDocument`. Save and close it.
- The returned `PdfDocument` supports save/close and basic page access — but document *creation* and
  *editing* features are commercial-only and not part of the CE.

Rules that always apply:
- **Always call `doc.Close()`** (or `using`/`Using`) — the document holds native resources.
- Set `converter.Options` **before** calling `Convert*`.
- The converter is **not thread-safe per instance** — use one converter per conversion.

### Rendering engines

Selectable via `converter.Options.RenderingEngine`:

| Engine | When to use | Notes |
|---|---|---|
| `WebKit` | **Default**, embedded — nothing extra to deploy | All supported frameworks |
| `WebKitRestricted` | Restricted-permission / limited-trust hosting | |
| `Blink` | Modern Chromium features over CDP | Needs the Blink runtime package; .NET Fx 4.6.1+ |
| `Chromium` | Latest rendering (CEF backend) | Needs the Chromium runtime package |

## What do you need to do? (routing)

| Task | Reference |
|---|---|
| Install, packages, pick/deploy a rendering engine | `reference/installation-and-engines.md` |
| Convert URL/HTML; page size, orientation, margins, viewport, auto-fit, delays/timeout, media type, page breaks, repeat table headers | `reference/html-to-pdf.md` |
| Headers & footers, page numbering | `reference/page-setup-headers-footers.md` |
| Web page authentication, cookies, custom HTTP headers, POST, proxy | `reference/auth-cookies-headers-post.md` |
| Deployment, Azure, the 5-page limit, troubleshooting common errors | `reference/deployment-limits-troubleshooting.md` |

## Gotchas (read before debugging)

- **`BadImageFormatException`** → x86/x64 package mismatch. Use the runtime that matches the process
  bitness (in IIS, the app pool's "Enable 32-Bit Applications" setting).
- **Only 5 pages came out** → that is the Community Edition cap, not a bug. Upgrade to `Select.Pdf`
  for unlimited pages.
- **Blank PDF / missing dynamic content** → give the page more time with `Options.MinPageLoadTime`,
  or (Blink/Chromium) a `WindowStatus` / `PostLoadingScript` signal. See `reference/html-to-pdf.md`.
- **Relative images/CSS not loading from an HTML string** → pass a base URL as the 2nd argument of
  `ConvertHtmlString(html, baseUrl)`.
- **Document not flushed / file locked** → you forgot `doc.Close()`.

## Faithfulness

Only use APIs, enums and options that appear in these reference files (sourced verbatim from the
SelectPdf Community Edition v26.3 SDK reference and official samples). Do not invent members or
options, and do not document commercial-only features (PDF creation/editing, forms, signatures,
extraction, accessibility) in this skill. Online docs: https://selectpdf.com/html-to-pdf/docs/.
