---
applyTo: "**/*.cs,**/*.vb"
---

# SelectPdf .NET library (commercial)

Apply when generating or manipulating PDFs with the commercial SelectPdf library
(`Select.Pdf` / `Select.Pdf.NetCore`). Self-hosted PDF toolkit for .NET, current as of v26.3. Full
depth (every topic, C# + VB.NET) is in the reference files linked at the bottom — open the relevant one
before writing non-trivial code.

**Wrong edition?** Free Community Edition (`Select.HtmlToPdf`, HTML→PDF only, 5-page cap) → use the
`selectpdf-lib-htmltopdf-net` instructions. Online REST API (`api.selectpdf.com`) → a `selectpdf-api-*`
file. Check the `.csproj`: `Select.Pdf*` = this, `Select.HtmlToPdf*` = Community.

## Install

Match the package to the runtime **and CPU architecture** (x86/x64 mismatch → `BadImageFormatException`):

```powershell
# .NET (Core / 5–10 / Standard 2.0)
Install-Package Select.Pdf.NetCore           # AnyCPU / x86
Install-Package Select.Pdf.NetCore.x64       # x64
# .NET Framework
Install-Package Select.Pdf                    # AnyCPU / x86
Install-Package Select.Pdf.x64               # x64
# Optional, only if using the Blink or Chromium engine
Install-Package Select.Pdf.NetCore.Blink
Install-Package Select.Pdf.NetCore.Chromium.Windows
```

Windows x86/x64 only (native engine deps); no Linux/macOS build. Blink needs .NET Fx 4.6.1+ / Core / 5–10.

## License key

Runs in trial mode with no key. Set once at startup, **before any other SelectPdf call**:

```csharp
SelectPdf.GlobalProperties.LicenseKey = "your-license-key";
```
```vbnet
SelectPdf.GlobalProperties.LicenseKey = "your-license-key"
```

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

- **Converters**: `HtmlToPdf` and `HtmlToImage`. Configure via `converter.Options` (`HtmlToPdfOptions`)
  **before** converting; `ConvertUrl(...)` / `ConvertHtmlString(...)` return a `PdfDocument`.
- **Object model**: build/load a `PdfDocument`, add `PdfPage`s, draw elements (`PdfTextElement`,
  `PdfImageElement`, `PdfHtmlElement`, graphics, templates), then `Save(...)` + `Close()`.
- Always `Close()` (or `using`). Set `GlobalProperties.LicenseKey` first. One converter per conversion
  (not thread-safe per instance).

Engines via `Options.RenderingEngine`: `WebKit` (default, embedded), `WebKitRestricted`, `Blink`
(needs Blink runtime; CDP), `Chromium` (needs Chromium runtime; required for tagged PDF/UA & PDF/A-3).

## Gotchas

- `BadImageFormatException` → x86/x64 package mismatch (IIS app pool "Enable 32-Bit Applications").
- Blank PDF / missing dynamic content → `Options.MinPageLoadTime`, or (Blink/Chromium) `WindowStatus` /
  `PostLoadingScript`.
- Relative images/CSS from an HTML string → pass base URL: `ConvertHtmlString(html, baseUrl)`.
- Tagged PDF/UA or PDF/A-3 → requires the **Chromium** (or Blink) engine and the **commercial** library.
- "Trial" markings → license key unset or set too late.
- File locked / not flushed → missing `doc.Close()`.

## Faithfulness

Only use APIs/options/enums documented in the reference files (sourced from the v26.3 SDK reference and
official samples). Don't invent members, overloads, enum values, or defaults.

## Full reference (open the relevant file)

Base: `https://github.com/selectpdf/ai-tools/blob/main/plugins/selectpdf/skills/selectpdf-lib-net/reference/`

- `installation-and-engines.md` — packages, engines, deployment
- `html-to-pdf.md` — convert URL/HTML/multiple; page setup, viewport, delays, media type, partial/exclude
- `page-setup-headers-footers.md` — headers/footers, page numbering, breaks, repeat table headers
- `links-bookmarks-toc.md` — links, bookmarks, table of contents, open actions
- `web-elements-and-auth.md` — element→PDF mapping, auth, cookies, headers, POST, proxy
- `pdf-creation-elements.md` — build PDFs: text, images, HTML elements, graphics, templates
- `edit-merge-split-stamp.md` — modify existing, merge, split, stamp/watermark, resize, portfolio
- `security-and-signatures.md` — encryption, permissions, viewer prefs, digital signatures
- `forms-and-extras.md` — AcroForms fill/create, compressor (Select.Pdf.Extras)
- `extract-text-image-search.md` — PDF→text, search, PDF→image
- `accessibility-and-compliance.md` — tagged PDF/UA-1, PDF/A-3, PDF/A, PDF/X (v26.3)
- `html-to-image.md` — render pages to PNG/JPG
- `deployment-azure-troubleshooting.md` — deployment, Azure, concurrency, troubleshooting
- `api-cheatsheet.md` — quick lookup of classes, enums, options
