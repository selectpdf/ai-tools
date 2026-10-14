---
applyTo: "**/*.cs,**/*.vb"
---

# SelectPdf HTML-to-PDF — Community Edition (free)

Apply when converting HTML/URLs to PDF with the free `Select.HtmlToPdf` Community Edition. Same
rendering engine as the commercial library, restricted to **HTML→PDF** and **5 pages per document**.
Current as of v26.5. Full depth (C# + VB.NET) is in the reference files linked below.

**Wrong edition?** Commercial (`Select.Pdf`), or need PDF creation/editing, forms, signatures,
extraction, or >5 pages → use the `selectpdf-lib-net` instructions. Online REST API → a
`selectpdf-api-*` file. Check the `.csproj`: `Select.HtmlToPdf*` = this, `Select.Pdf*` = commercial.

**Cross-platform?** Linux, macOS on Apple Silicon, Docker, `SelectPdf.HtmlToPdf.Universal`, or `using SelectPdf.Universal;` → use `selectpdf-lib-htmltopdf-universal-net` instead (same 5-page limit).

## Install

Match the package to the runtime **and CPU architecture** (x86/x64 mismatch → `BadImageFormatException`):

```powershell
# .NET (Core / 5–10 / Standard 2.0)
Install-Package Select.HtmlToPdf.NetCore
# .NET Framework
Install-Package Select.HtmlToPdf
# Optional, only if using the Blink or Chromium engine
Install-Package Select.HtmlToPdf.NetCore.Blink
Install-Package Select.HtmlToPdf.NetCore.Chromium.Windows
```

Classic packages: Windows x86/x64 only. For Linux (x64, ARM64), macOS on Apple Silicon or Docker → the free `SelectPdf.HtmlToPdf.Universal` package (`selectpdf-lib-htmltopdf-universal-net`). Blink needs .NET Fx 4.6.1+ / Core / 5–10.

## The 5-page limit

The CE renders **at most 5 pages per document**; content past page 5 is silently dropped. It is free
for commercial use within this limit. To remove the cap, upgrade to the commercial `Select.Pdf` library
(identical API — see `selectpdf-lib-net`). The CE `GlobalProperties` class has **no `LicenseKey`
member**; licensing is a commercial concept.

## Quickstart

```csharp
using SelectPdf;

HtmlToPdf converter = new HtmlToPdf();
PdfDocument doc = converter.ConvertUrl("https://selectpdf.com");   // or ConvertHtmlString(html, baseUrl)
doc.Save("output.pdf");
doc.Close();   // always close — holds native resources
```
```vbnet
Imports SelectPdf

Dim converter As New HtmlToPdf()
Dim doc As PdfDocument = converter.ConvertUrl("https://selectpdf.com")   ' or ConvertHtmlString(html, baseUrl)
doc.Save("output.pdf")
doc.Close()   ' always close — holds native resources
```

## Mental model

- One class: **`HtmlToPdf`**. Configure via `converter.Options` (`HtmlToPdfOptions`) **before**
  converting; `ConvertUrl(...)` / `ConvertHtmlString(...)` return a `PdfDocument`. Save and `Close()`.
- Document *creation*/*editing* features are commercial-only and not in the CE.
- Always `Close()` (or `using`). One converter per conversion (not thread-safe per instance).

Engines via `Options.RenderingEngine`: `WebKit` (default, embedded), `WebKitRestricted`, `Blink`
(needs Blink runtime), `Chromium` (needs Chromium runtime).

## Gotchas

- `BadImageFormatException` → x86/x64 package mismatch (IIS app pool "Enable 32-Bit Applications").
- Only 5 pages came out → the CE cap, not a bug. Upgrade to `Select.Pdf` for unlimited pages.
- Blank PDF / missing dynamic content → `Options.MinPageLoadTime`, or (Blink/Chromium) `WindowStatus` /
  `PostLoadingScript`.
- Relative images/CSS from an HTML string → pass base URL: `ConvertHtmlString(html, baseUrl)`.
- File locked / not flushed → missing `doc.Close()`.
- Page needs WebGL → off by default since v26.4; set `Options.WebGlEnabled = true`.

## Faithfulness

Only use APIs/options/enums documented in the reference files (sourced from the v26.3 CE SDK reference, updated for v26.4 and v26.5,
and samples). Don't invent members or defaults, and don't use commercial-only features (PDF
creation/editing, forms, signatures, extraction, accessibility).

## Full reference (open the relevant file)

Base: `https://github.com/selectpdf/ai-tools/blob/main/plugins/selectpdf/skills/selectpdf-lib-htmltopdf-net/reference/`

- `installation-and-engines.md` — packages, engines, deployment
- `html-to-pdf.md` — convert URL/HTML; page setup, viewport, delays, media type, breaks, links, bookmarks, doc properties, viewer prefs
- `page-setup-headers-footers.md` — headers/footers, page numbering
- `auth-cookies-headers-post.md` — authentication, cookies, HTTP headers, POST, proxy
- `deployment-limits-troubleshooting.md` — deployment, Azure, the 5-page limit, troubleshooting
