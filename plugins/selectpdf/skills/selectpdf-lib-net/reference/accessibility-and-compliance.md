# Accessible (tagged) PDF and compliance — PDF/UA, PDF/A, PDF/X

Generate tagged accessible PDF (PDF/UA-1), PDF/A archival documents (including PDF/A-3), and PDF/X output from HTML with the commercial Select.Pdf .NET library.

Tagged (accessible) PDF and PDF/A-3 output use the **Chromium** or **Blink** rendering engine — only those engines emit the logical structure tree, so set `HtmlToPdfOptions.RenderingEngine` to `RenderingEngine.Chromium` (or `RenderingEngine.Blink`); the default `WebKit` engine raises an error when `Tagged` or `PdfStandard.PdfA3A` is requested. These are **commercial-library features**, not available in the free Community Edition. Accessible tagged PDF / PDF/UA-1 and PDF/A-3 (levels b, u, a) are **new in v26.3** ("2026 Vol 3"); PDF/A and PDF/X were already supported. Output conformance is validated with veraPDF.

## Generating accessible tagged PDF / PDF/UA-1 from HTML

Set `HtmlToPdfOptions.Tagged` to `true` to produce a tagged, accessible (PDF/UA) document. The converter takes the logical structure tree — headings, paragraphs, lists, tables, figures with alternate text, links, and the reading order — directly from the rendering engine's accessible output, and marks decorative content (backgrounds, rules, watermarks) as artifacts so assistive technologies skip it. Set `HtmlToPdfOptions.Title` and `HtmlToPdfOptions.Language` (both required for an accessible PDF) to describe the document. Tagging requires the Chromium or Blink engine; accessibility quality depends on the source HTML (images need `alt`, headings used in order, proper table markup, etc.).

```csharp
HtmlToPdf converter = new HtmlToPdf();

// tagged / PDF/UA output requires the Blink or Chromium engine
converter.Options.RenderingEngine = RenderingEngine.Chromium;

// produce a tagged, accessible (PDF/UA) document
converter.Options.Tagged = true;
converter.Options.Title = "Quarterly Report";
converter.Options.Language = "en-US";

PdfDocument doc = converter.ConvertUrl("https://www.example.com");
doc.Save("accessible.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

' tagged / PDF/UA output requires the Blink or Chromium engine
converter.Options.RenderingEngine = RenderingEngine.Chromium

' produce a tagged, accessible (PDF/UA) document
converter.Options.Tagged = True
converter.Options.Title = "Quarterly Report"
converter.Options.Language = "en-US"

Dim doc As PdfDocument = converter.ConvertUrl("https://www.example.com")
doc.Save("accessible.pdf")
doc.Close()
```

## PDF/A and PDF/A-3 archiving

Set `HtmlToPdfOptions.PdfStandard` to a `PdfStandard` value to produce archival output. The archival values include `PdfStandard.PdfA` (PDF/A-1b), `PdfStandard.PdfA1B`, `PdfStandard.PdfA2B` (PDF/A-2b), and the PDF/A-3 family `PdfStandard.PdfA3B` (level B, visual reproduction), `PdfStandard.PdfA3U` (level U, all text has Unicode mapping), and `PdfStandard.PdfA3A` (level A, accessible — includes the tagged logical structure). Selecting `PdfA3A` automatically produces a tagged document and therefore requires the Chromium or Blink engine; levels B and U are produced by every engine. PDF/A does not permit certain interactive features (sound, movie, and screen annotations, and link actions that launch a file or run JavaScript) — adding one raises an error naming the unsupported feature.

```csharp
HtmlToPdf converter = new HtmlToPdf();

// PDF/A-3A is archival AND accessible (tagging is enabled automatically);
// like all tagged output it requires the Blink or Chromium engine
converter.Options.RenderingEngine = RenderingEngine.Chromium;
converter.Options.PdfStandard = PdfStandard.PdfA3A;
converter.Options.Title = "Invoice 2026-0042";
converter.Options.Language = "en-US";

PdfDocument doc = converter.ConvertUrl("https://www.example.com/invoice");
doc.Save("invoice-pdfa3a.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

' PDF/A-3A is archival AND accessible (tagging is enabled automatically);
' like all tagged output it requires the Blink or Chromium engine
converter.Options.RenderingEngine = RenderingEngine.Chromium
converter.Options.PdfStandard = PdfStandard.PdfA3A
converter.Options.Title = "Invoice 2026-0042"
converter.Options.Language = "en-US"

Dim doc As PdfDocument = converter.ConvertUrl("https://www.example.com/invoice")
doc.Save("invoice-pdfa3a.pdf")
doc.Close()
```

## PDF/X

For print-production output, set `HtmlToPdfOptions.PdfStandard` to the PDF/X value `PdfStandard.PdfX1A`. PDF/X commonly pairs with a CMYK color space (see below). To produce a non-compliant standard PDF, leave `PdfStandard` at its default `PdfStandard.Full`.

```csharp
HtmlToPdf converter = new HtmlToPdf();
converter.Options.PdfStandard = PdfStandard.PdfX1A;

PdfDocument doc = converter.ConvertUrl("https://www.example.com");
doc.Save("print-pdfx1a.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()
converter.Options.PdfStandard = PdfStandard.PdfX1A

Dim doc As PdfDocument = converter.ConvertUrl("https://www.example.com")
doc.Save("print-pdfx1a.pdf")
doc.Close()
```

## Color space for compliance

Set `HtmlToPdfOptions.ColorSpace` (a `PdfColorSpace` value) to control the color space of the generated document. The available values are `RGB`, `CMYK`, and `GrayScale`. Use `CMYK` for print/PDF-X workflows that require it.

```csharp
HtmlToPdf converter = new HtmlToPdf();
converter.Options.PdfStandard = PdfStandard.PdfX1A;
converter.Options.ColorSpace = PdfColorSpace.CMYK;

PdfDocument doc = converter.ConvertUrl("https://www.example.com");
doc.Save("print-cmyk.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()
converter.Options.PdfStandard = PdfStandard.PdfX1A
converter.Options.ColorSpace = PdfColorSpace.CMYK

Dim doc As PdfDocument = converter.ConvertUrl("https://www.example.com")
doc.Save("print-cmyk.pdf")
doc.Close()
```

See also: html-to-pdf.md, installation-and-engines.md, api-cheatsheet.md
