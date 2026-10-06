---
name: selectpdf-lib-htmltopdf-universal-net
description: >-
  Convert HTML and URLs to PDF in .NET (C# / VB.NET) with SelectPdf.HtmlToPdf.Universal, the free
  cross-platform SelectPdf Community Edition (SelectPdf.HtmlToPdf.Universal plus
  SelectPdf.Universal.Native.<rid> NuGet packages, namespace SelectPdf.Universal) on Windows, Linux (x64,
  ARM64), macOS on Apple Silicon and Docker. Use for HTML-to-PDF with Chromium (sync or async) with page
  setup, headers/footers, page numbering, links, bookmarks, web authentication, cookies, HTTP headers and
  proxy. Free for commercial use, capped at 5 pages per document. For PDF creation/editing, Word to PDF,
  forms, signatures, redaction or longer output use selectpdf-lib-universal-net; for the classic Windows
  edition use selectpdf-lib-htmltopdf-net; for the online REST API use a selectpdf-api-* skill.
---

# SelectPdf.HtmlToPdf.Universal — Community Edition (free, cross-platform)

Expert guidance for **SelectPdf.HtmlToPdf.Universal**, the free cross-platform HTML to PDF converter:
Windows (x64, x86), Linux (x64, ARM64), macOS on Apple Silicon and Docker, rendering with Chromium.
HTML→PDF only, **5 pages per document**. Current as of **v26.4**.

## Pick the right skill first

- **Free cross-platform Community Edition (this skill)** — `SelectPdf.HtmlToPdf.Universal` +
  `SelectPdf.Universal.Native.<rid>`; code uses `using SelectPdf.Universal;`.
- **Full cross-platform library** — documents over 5 pages, HTML to image, PDF creation or editing, Word to
  PDF, forms, signatures, redaction, tagged PDF/UA, PDF/A-3A or e-invoices → **`selectpdf-lib-universal-net`**.
  It uses the same namespace and the same code, so moving up is a package swap.
- **Classic Windows Community Edition** — `Select.HtmlToPdf` / `Select.HtmlToPdf.NetCore`, code uses
  `using SelectPdf;` → **`selectpdf-lib-htmltopdf-net`**. To port classic code, read
  `reference/migrating-from-classic.md`.
- **Online REST API** — the project calls `api.selectpdf.com` over HTTP → a `selectpdf-api-*` skill.

## Install

The converter plus the native engine for **each runtime you deploy to**:

```bash
dotnet add package SelectPdf.HtmlToPdf.Universal
dotnet add package SelectPdf.Universal.Native.linux-x64
```

Native packages: `win-x64`, `win-x86`, `linux-x64`, `linux-arm64`, `osx-arm64` (macOS 13 or newer), the same
ones the full library uses. Pick the one matching the **architecture of the process that converts**. The
library targets .NET 8, .NET 10 and .NET Standard 2.0 (usable from .NET Framework 4.6.1+). Optional:
`SelectPdf.Universal.Fonts` adds Noto fonts for every script, for Linux hosts and containers with no fonts.

> **Platform limits:** glibc-based Linux only (glibc 2.30+; Alpine/musl is not supported). No HTML-to-PDF on
> Intel Macs (no `osx-x64` engine), and no Windows-on-ARM engine.

## License

Free for personal and commercial use; no license key is needed, and `GlobalProperties` has no `LicenseKey`
property in this edition. The only limit is **5 pages per document**.

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

Rules that always apply:
- `using SelectPdf.Universal;` — **no `System.Drawing`** in the API.
- **Chromium is the only engine**; never set `WebKit`, `WebKitRestricted` or `Blink`.
- Always `Close()` documents. One converter per conversion; conversions running at once are limited by
  `HtmlToPdfOptions.MaximumConcurrentConversions` (default 8).

## What do you need to do? (routing)

| Task | Reference |
|---|---|
| Packages, license, the Chromium engine, fonts and languages, deployment to Windows, Linux, macOS, Docker, Azure, AWS, Google Cloud | `reference/installation-and-deployment.md` |
| Port code from the classic `Select.HtmlToPdf` edition | `reference/migrating-from-classic.md` |
| Convert URL / HTML; async and cancellation; page settings, delays, media type, page breaks, table headers, links, automatic bookmarks, document properties, viewer preferences, security, other options | `reference/html-to-pdf.md` |
| Headers, footers, page numbering | `reference/page-setup-headers-footers.md` |
| Web page authentication, cookies, HTTP headers, POST, proxy | `reference/auth-cookies-headers-post.md` |
| Troubleshooting, security recommendations for untrusted input | `reference/troubleshooting-and-security.md` |

## Gotchas (read before debugging)

- **Only 5 pages came out** → that is the Community Edition limit, not a bug. The full
  `SelectPdf.Universal` package removes it with the same code.
- **Classic code does not compile** → it uses `using SelectPdf;`, `System.Drawing` types or WebKit/Blink
  settings. See `reference/migrating-from-classic.md`.
- **`HtmlToImage`, `PdfStandard.PdfA3A`, partial conversion or open actions are missing** → those are in the
  full `SelectPdf.Universal` package only.
- **`MinPageLoadTime` / `MaxPageLoadTime`** still compile but are obsolete: use `ConversionDelay` and
  `NavigationTimeout`.
- **Engine not found on Linux or macOS** → the native package for that runtime is missing, or it does not
  match the process architecture.
- **Boxes or wrong glyphs on Linux** → the host has no fonts for that script. Add `SelectPdf.Universal.Fonts`
  or load web fonts.
- **Page needs WebGL** → WebGL is off by default; set `Options.WebGlEnabled = true`.

## Faithfulness

The reference files are converted from the shipped SelectPdf.HtmlToPdf.Universal v26.4 help topics, with the
official C# and VB.NET samples; every sample was compiled against the SelectPdf.HtmlToPdf.Universal 26.4.0
NuGet package. Only use APIs, enums and options that appear in them, and do not document full-library
features in this skill. Online documentation: https://selectpdf.com/html-to-pdf/docs/.
