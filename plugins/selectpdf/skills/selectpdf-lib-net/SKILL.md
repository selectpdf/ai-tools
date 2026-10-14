---
name: selectpdf-lib-net
description: >-
  Generate, edit and render PDFs in .NET (C# / VB.NET) with the commercial Select.Pdf library
  (Select.Pdf or Select.Pdf.NetCore NuGet packages). Use for HTML-to-PDF conversion, building PDFs
  from text/images/shapes/tables/templates, merging/splitting/stamping existing PDFs, AcroForms and
  digital signatures, encryption and permissions, PDF-to-text and PDF-to-image extraction, and
  accessible tagged PDF/UA-1 and PDF/A-3 output, and ZUGFeRD / Factur-X e-invoices. Trigger whenever a
  .NET app on Windows uses the classic SelectPdf library (namespace SelectPdf). For Linux, macOS,
  Docker or the SelectPdf.Universal namespace, use selectpdf-lib-universal-net instead. This is the
  local library, NOT the online REST API (for that, see the selectpdf-api-* skills).
---

# SelectPdf .NET Library (commercial)

Expert guidance for the **commercial Select.Pdf** library — the full, self-hosted PDF toolkit for
.NET on Windows. No external service and no print drivers. Current as of **v26.5**.

## Pick the right skill first

- **Commercial, full features (this skill)** — `Select.Pdf` / `Select.Pdf.NetCore`. HTML→PDF *plus*
  PDF creation, editing, forms, signatures, security, extraction, accessibility/compliance.
- **Free Community Edition** — if the project uses `Select.HtmlToPdf` / `Select.HtmlToPdf.NetCore`
  (HTML→PDF only, capped at 5 pages), use the **`selectpdf-lib-htmltopdf-net`** skill instead.
- **Cross-platform (SelectPdf.Universal)** — if the project references `SelectPdf.Universal` /
  `SelectPdf.Universal.Native.*`, uses `using SelectPdf.Universal;`, or must run on **Linux, macOS on
  Apple Silicon or Docker**, use the **`selectpdf-lib-universal-net`** skill (free edition:
  `selectpdf-lib-htmltopdf-universal-net`). The APIs are close but not identical; do not mix them.
- **Online REST API** — if the project calls `api.selectpdf.com` over HTTP (any language), this is
  the wrong skill; use a `selectpdf-api-*` skill.

If you are unsure which package is referenced, check the project's `.csproj`/`packages.config` for
`Select.Pdf*` (commercial) vs `Select.HtmlToPdf*` (community).

## Install

Pick the package that matches the target runtime **and CPU architecture** — an x86/x64 mismatch is
the most common cause of `BadImageFormatException`.

**.NET (Core / 5–10 / Standard 2.0):**

```powershell
Install-Package Select.Pdf.NetCore          # AnyCPU / x86
Install-Package Select.Pdf.NetCore.x64      # x64
```

**.NET Framework (2.0, 4.x):**

```powershell
Install-Package Select.Pdf                   # AnyCPU / x86
Install-Package Select.Pdf.x64               # x64
```

**Optional engine runtimes** — only needed when you select the Blink or Chromium rendering engine:

```powershell
Install-Package Select.Pdf.NetCore.Blink
Install-Package Select.Pdf.NetCore.Chromium.Windows
```

> **Platform:** the classic packages run on **Windows x86/x64 only**. For Linux (x64, ARM64), macOS on
> Apple Silicon or Docker, use **SelectPdf.Universal** (see `selectpdf-lib-universal-net`); one license
> covers both. The Blink engine requires .NET Framework 4.6.1+ (or .NET Core / 5–10).

## License key

The library runs in **trial mode** with no key. Unlock the full feature set (no page cap, no trial
markings) by setting the key **once at startup, before creating any converter or document**:

```csharp
SelectPdf.GlobalProperties.LicenseKey = "your-license-key";
```
```vbnet
SelectPdf.GlobalProperties.LicenseKey = "your-license-key"
```

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
string html = "<h1>Hello, PDF!</h1><p>Generated with Select.Pdf.</p>";
PdfDocument doc = converter.ConvertHtmlString(html);   // pass a base URL as 2nd arg to resolve relative links/images
doc.Save("hello.pdf");
doc.Close();
```
```vbnet
Imports SelectPdf

Dim converter As New HtmlToPdf()
Dim html As String = "<h1>Hello, PDF!</h1><p>Generated with Select.Pdf.</p>"
Dim doc As PdfDocument = converter.ConvertHtmlString(html)   ' pass a base URL as 2nd arg to resolve relative links/images
doc.Save("hello.pdf")
doc.Close()
```

## Mental model

The library has two halves that meet at `PdfDocument`:

1. **HTML→PDF / HTML→Image converters** — `HtmlToPdf` and `HtmlToImage`. Configure everything
   through `converter.Options` (an `HtmlToPdfOptions`); call `ConvertUrl(...)` /
   `ConvertHtmlString(...)`; get back a `PdfDocument`.
2. **The PDF object model** — `PdfDocument` with `Pages`, and `PdfPage` onto which you draw elements
   (`PdfTextElement`, `PdfImageElement`, `PdfHtmlElement`, graphics, tables, templates). You can
   create a document from scratch, or load/modify an existing one, then `Save(...)` and `Close()`.

Rules that always apply:
- **Always call `doc.Close()`** (or `Using`/`using`) — the document holds native resources.
- Set `converter.Options` **before** calling `Convert*`.
- Set `GlobalProperties.LicenseKey` **once, first**, before any other SelectPdf call.
- The converter is **not thread-safe per instance** — use one converter per conversion; for servers
  see `reference/deployment-azure-troubleshooting.md` for concurrency guidance.

### Rendering engines

Selectable per conversion via `converter.Options.RenderingEngine`:

| Engine | When to use | Notes |
|---|---|---|
| `WebKit` | **Default**, embedded — no extra files to deploy | Runs on all supported frameworks |
| `WebKitRestricted` | Limited-trust / restricted-permission hosting | |
| `Blink` | Modern Chromium features over CDP | Needs `Select.Pdf.NetCore.Blink`; .NET Fx 4.6.1+ |
| `Chromium` | Latest rendering (CEF backend); required for tagged PDF/UA & PDF/A-3 | Needs `Select.Pdf.NetCore.Chromium.Windows` |

## What do you need to do? (routing)

Load the matching reference file for full prose, options and C#/VB samples.

| Task | Reference |
|---|---|
| Install, packages, pick/deploy a rendering engine | `reference/installation-and-engines.md` |
| Convert URL/HTML/multiple URLs; page size, margins, viewport, auto-fit, delays, media type, JS, partial/exclude | `reference/html-to-pdf.md` |
| Headers & footers, page numbering, page breaks, repeat table headers | `reference/page-setup-headers-footers.md` |
| Internal/external links, bookmarks (manual + automatic), table of contents, open actions | `reference/links-bookmarks-toc.md` |
| Map HTML elements to PDF coordinates; web auth, cookies, custom HTTP headers, POST, proxy | `reference/web-elements-and-auth.md` |
| Build a PDF from scratch — text, images, HTML elements, shapes/graphics, templates, doc properties | `reference/pdf-creation-elements.md` |
| Modify existing PDFs — merge, split, stamp/watermark, resize, portfolio, add headers/footers | `reference/edit-merge-split-stamp.md` |
| Encryption, passwords, permissions, viewer preferences, digital signatures | `reference/security-and-signatures.md` |
| Create and fill AcroForms; PDF compression (Select.Pdf.Extras) | `reference/forms-and-extras.md` |
| Extract text from a PDF, search text, render PDF pages to images | `reference/extract-text-image-search.md` |
| Accessible tagged PDF / PDF/UA-1, PDF/A-3, PDF/A, PDF/X; ZUGFeRD / Factur-X e-invoices and custom XMP metadata (v26.4) | `reference/accessibility-and-compliance.md` |
| Render a web page to PNG/JPG | `reference/html-to-image.md` |
| Deployment, Azure, performance/concurrency, troubleshooting common errors | `reference/deployment-azure-troubleshooting.md` |
| Quick lookup of key classes, enums and option names | `reference/api-cheatsheet.md` |

## Gotchas (read before debugging)

- **`BadImageFormatException`** → x86/x64 package mismatch. Install the `.x64` variant for 64-bit
  processes (IIS app pool "Enable 32-Bit Applications" = False, or a 64-bit console/worker).
- **Blank/empty PDF or missing dynamic content** → the page needs more time or a JS signal. Use
  `Options.MinPageLoadTime`, or (Blink/Chromium) `WindowStatus` / `PostLoadingScript`. See
  `reference/html-to-pdf.md`.
- **Relative images/CSS not loading from an HTML string** → pass a base URL as the 2nd argument of
  `ConvertHtmlString(html, baseUrl)`.
- **Tagged PDF/UA or PDF/A-3 output** → requires the **Chromium** (or Blink) engine and the
  **commercial** library; not available in the Community Edition.
- **"Trial" markings / 5-page cap** → license key not set, or set too late. Set
  `GlobalProperties.LicenseKey` before any other SelectPdf call.
- **Document not flushed / file locked** → you forgot `doc.Close()`.
- **Page needs WebGL (3D charts, maps)** → WebGL is off by default since v26.4; set
  `Options.WebGlEnabled = true`.
- **ZUGFeRD / Factur-X invoice** → the document must be PDF/A-3 (`Options.PdfStandard = PdfStandard.PdfA3B`
  or `PdfA3U`/`PdfA3A`) before calling `doc.AddZugferdInvoice(...)`. See
  `reference/accessibility-and-compliance.md`.

## Faithfulness

Only use APIs, enums and options that appear in these reference files (sourced verbatim from the
SelectPdf v26.3 SDK reference and official samples, updated for v26.4 and v26.5 from the SDK source). Do not
invent members, overloads or option names. When in doubt, consult `reference/api-cheatsheet.md` or the
online docs at https://selectpdf.com/pdf-library-classic/.
