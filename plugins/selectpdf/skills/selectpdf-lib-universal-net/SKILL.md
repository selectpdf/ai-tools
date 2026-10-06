---
name: selectpdf-lib-universal-net
description: >-
  Generate, edit and render PDFs in .NET (C# / VB.NET) with SelectPdf.Universal, the cross-platform
  commercial SelectPdf library (SelectPdf.Universal plus SelectPdf.Universal.Native.<rid> NuGet packages,
  namespace SelectPdf.Universal) on Windows, Linux (x64, ARM64), macOS on Apple Silicon and Docker. Use for
  HTML-to-PDF and HTML-to-image with Chromium (sync or async), Word to PDF, building PDFs from text, images,
  shapes and templates, merging and stamping existing PDFs, AcroForms, digital signatures and signature
  validation, redaction, encryption (including AES-GCM and AES-256 R6), PDF-to-text and PDF-to-image, tagged
  PDF/UA-1 and PDF/UA-2, PDF/A up to PDF/A-4, and ZUGFeRD / Factur-X e-invoices. Trigger whenever a .NET app
  uses SelectPdf.Universal or needs SelectPdf on Linux, macOS or in containers. This is the local library,
  NOT the online REST API (for that, see the selectpdf-api-* skills).
---

# SelectPdf.Universal (commercial, cross-platform)

Expert guidance for **SelectPdf.Universal**, the cross-platform SelectPdf library for .NET: Windows (x64,
x86), Linux (x64, ARM64), macOS on Apple Silicon and Docker containers. HTML is rendered with Chromium.
Current as of **v26.4**.

## Pick the right skill first

- **SelectPdf.Universal, commercial (this skill)** — packages `SelectPdf.Universal` +
  `SelectPdf.Universal.Native.<rid>`; code uses `using SelectPdf.Universal;`.
- **Free cross-platform Community Edition** — `SelectPdf.HtmlToPdf.Universal` (HTML→PDF only, 5 pages per
  document) → use **`selectpdf-lib-htmltopdf-universal-net`**.
- **Classic Windows library** — `Select.Pdf` / `Select.Pdf.NetCore`, code uses `using SelectPdf;` → use
  **`selectpdf-lib-net`** (free classic edition: `selectpdf-lib-htmltopdf-net`). The APIs are close but not
  identical: do not mix classic and Universal code. To port classic code, read `reference/migrating-from-classic.md`.
- **Online REST API** — the project calls `api.selectpdf.com` over HTTP → a `selectpdf-api-*` skill.

Check the `.csproj`: `SelectPdf.Universal*` = this skill; `SelectPdf.HtmlToPdf.Universal` = the free
Universal edition; `Select.Pdf*` / `Select.HtmlToPdf*` = the classic skills.

## Install

Two packages: the managed library (AnyCPU) and the native engine for **each runtime you deploy to**:

```bash
dotnet add package SelectPdf.Universal
dotnet add package SelectPdf.Universal.Native.linux-x64
```

Native packages: `win-x64`, `win-x86`, `linux-x64`, `linux-arm64`, `osx-arm64` (macOS 13 or newer). Pick the
one matching the **architecture of the process that converts**; a build for several platforms references
several native packages side by side. The library targets .NET 8, .NET 10 and .NET Standard 2.0 (usable from
.NET Framework 4.6.1+). Optional: `SelectPdf.Universal.Fonts` adds Noto fonts for every script, for Linux
hosts and containers with no fonts installed.

> **Platform limits:** glibc-based Linux only (glibc 2.30+; Alpine/musl is not supported). On an Intel Mac
> the managed features work, but HTML-to-PDF, PDF-to-text and PDF-to-image do not (no `osx-x64` engine).
> There is no Windows-on-ARM engine.

## License key

The library runs in **trial mode** (watermarked output) with no key. Set the key once, at startup, before
any other SelectPdf call:

```csharp
SelectPdf.Universal.GlobalProperties.LicenseKey = "your-license-key";
```
```vbnet
SelectPdf.Universal.GlobalProperties.LicenseKey = "your-license-key"
```

One license covers SelectPdf.Universal and the classic Windows library. License errors throw `LicenseException`.

## Quickstart

```csharp
using SelectPdf.Universal;

// the same code on Windows, Linux and macOS
HtmlToPdf converter = new HtmlToPdf();
PdfDocument doc = converter.ConvertUrl("https://selectpdf.com");
doc.Save("output.pdf");
doc.Close();
```
```vbnet
Imports SelectPdf.Universal

' the same code on Windows, Linux and macOS
Dim converter As New HtmlToPdf()
Dim doc As PdfDocument = converter.ConvertUrl("https://selectpdf.com")
doc.Save("output.pdf")
doc.Close()
```

`ConvertHtmlString(html, baseUrl)` converts an HTML string. `ConvertUrlAsync` / `ConvertHtmlStringAsync`
(optionally with a `CancellationToken`) convert without holding a thread; `doc.Save()` with no arguments
returns a `byte[]`.

## Mental model

1. **Converters** — `HtmlToPdf`, `HtmlToImage` (returns encoded image bytes), `WordToPdf`. Configure through
   `converter.Options`; get back a `PdfDocument` (or image bytes).
2. **The PDF object model** — `PdfDocument` / `PdfPage` with elements (`PdfTextElement`, `PdfImageElement`,
   `PdfHtmlElement`, `PdfWordElement`, graphics, templates). Create from scratch or load an existing file, then
   `Save(...)` and `Close()`.
3. **Managers for existing files** — `PdfRedactionManager`, `PdfFormManager`, `PdfSecurityManager`,
   `PdfResizeManager`, `PdfPortfolioManager`: `Load`, work, `Save`.

Rules that always apply:
- `using SelectPdf.Universal;` (and `SelectPdf.Universal.Drawing` for `RectangleF`, `PointF`, `SizeF`, `Color`).
  **No `System.Drawing`** anywhere in the API: colors are `PdfColor`, images are paths, streams or byte arrays.
- **Chromium is the only engine.** `RenderingEngine` exists but its only value is `Chromium`; never set
  `WebKit`, `WebKitRestricted` or `Blink`.
- Always `Close()` documents (or use `using`).
- One converter per conversion. Conversions running at once are limited by
  `HtmlToPdfOptions.MaximumConcurrentConversions` (default 8); extra ones queue.

## What do you need to do? (routing)

| Task | Reference |
|---|---|
| Packages, license key, the Chromium engine, fonts and languages, deployment to Windows, Linux, macOS, Docker, Azure, AWS, Google Cloud | `reference/installation-and-deployment.md` |
| Port code from the classic `Select.Pdf` library (namespace, System.Drawing, removed and renamed members, changed defaults) | `reference/migrating-from-classic.md` |
| Convert URL / HTML / multiple URLs; async and cancellation; page settings, delays, media type, partial / exclude, page breaks, table headers, document properties, viewer preferences, security, other options | `reference/html-to-pdf.md` |
| Headers, footers, page numbering, headers/footers on existing PDFs | `reference/page-setup-headers-footers.md` |
| Links, bookmarks, table of contents, open actions | `reference/links-bookmarks-toc.md` |
| Element positions in the PDF; authentication, cookies, HTTP headers, POST, proxy | `reference/web-elements-and-auth.md` |
| Build a PDF from scratch: text, images, HTML elements, graphics, page settings, templates | `reference/pdf-creation-elements.md` |
| Modify existing PDFs: merge (`PdfDocument.Append`), resize, stamps and watermarks, portfolios, compression | `reference/edit-merge-split-stamp.md` |
| Encryption (RC4, AES, AES-GCM, AES-256 R6), permissions, digital signatures and signature validation | `reference/security-and-signatures.md` |
| Fill PDF forms | `reference/forms.md` |
| PDF to text, text search, PDF to image | `reference/extract-text-image-search.md` |
| Tagged PDF/UA-1 and PDF/UA-2, PDF/A (up to PDF/A-4), ZUGFeRD / Factur-X invoices | `reference/accessibility-and-compliance.md` |
| Word (DOCX, DOC, RTF, Markdown and more) to PDF; Word documents as PDF elements | `reference/word-to-pdf.md` |
| Remove content by area, text or regular expression | `reference/redaction.md` |
| Render a web page or HTML to an image | `reference/html-to-image.md` |
| Troubleshooting, security recommendations for untrusted input | `reference/troubleshooting-and-security.md` |

## Gotchas (read before debugging)

- **Classic code does not compile** → it uses `using SelectPdf;`, `System.Drawing` types, WebKit/Blink
  settings or removed classes (`PdfMergeManager`, `PdfPrinter`, `Select.Pdf.Extras`). See
  `reference/migrating-from-classic.md`.
- **`MinPageLoadTime` / `MaxPageLoadTime` / `VisibleWebElementId`** still compile but are obsolete: use
  `ConversionDelay`, `NavigationTimeout` and `VisibleWebElementSelector`.
- **Engine not found / conversion fails on Linux or macOS** → the native package for that runtime is missing,
  or it does not match the process architecture. Reference the right `SelectPdf.Universal.Native.<rid>`.
- **Boxes or wrong glyphs on Linux** → the host has no fonts for that script. Add `SelectPdf.Universal.Fonts`
  or load web fonts. A font requested by name that is not installed (e.g. "Arial" on Linux) is an error, not a
  silent substitution: use the bundled **Liberation Sans / Serif / Mono**, which have Arial / Times New Roman /
  Courier New metrics.
- **Output differs from the classic library** → classic defaulted to WebKit; Universal always renders with
  Chromium. Control pagination with CSS (`break-before`, `break-after`, `break-inside: avoid`).
- **Encrypted PDF will not open in an old reader** → a password now means AES-256 (revision 6) by default; set
  `KeySize` / `Algorithm` to AES 128-bit for readers older than Acrobat X.
- **Several users' pages share cached content** → the HTTP cache is kept between conversions by default; a
  multi-user service should set `HttpCache = HttpCacheMode.Clear`.
- **Page needs WebGL** → WebGL is off by default; set `Options.WebGlEnabled = true`.
- **PDF/UA-2** pairs only with **PDF/A-4** (the PDF 2.0 archival level); both standards require a document
  title and language.

## Faithfulness

The reference files are converted from the shipped SelectPdf.Universal v26.4 help topics, with the official
C# and VB.NET samples; every sample was compiled against the SelectPdf.Universal 26.4.0 NuGet package. Only
use APIs, enums and options that appear in them. Do not invent members, and do not carry classic-only members
over. Online documentation: https://selectpdf.com/pdf-library/.
