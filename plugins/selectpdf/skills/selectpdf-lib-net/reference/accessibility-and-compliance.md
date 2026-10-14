# Accessible (tagged) PDF and compliance — PDF/UA, PDF/A, PDF/X, e-invoices

Generate tagged accessible PDF (PDF/UA-1), PDF/A archival documents (including PDF/A-3), and PDF/X output from HTML with the commercial Select.Pdf .NET library.

Tagged (accessible) PDF and PDF/A-3 output use the **Chromium** or **Blink** rendering engine — only those engines emit the logical structure tree, so set `HtmlToPdfOptions.RenderingEngine` to `RenderingEngine.Chromium` (or `RenderingEngine.Blink`); the default `WebKit` engine raises an error when `Tagged` or `PdfStandard.PdfA3A` is requested. These are **commercial-library features**, not available in the free Community Edition. Accessible tagged PDF / PDF/UA-1 and PDF/A-3 (levels b, u, a) are **new in v26.3** ("2026 Vol 3"); PDF/A and PDF/X were already supported. Output conformance is validated with veraPDF.

## Generating accessible tagged PDF / PDF/UA-1 from HTML

Set `HtmlToPdfOptions.Tagged` to `true` to produce a tagged, accessible (PDF/UA) document. The converter takes the logical structure tree — headings, paragraphs, lists, tables, figures with alternate text, links, and the reading order — directly from the rendering engine's accessible output, and marks decorative content (backgrounds, rules, watermarks) as artifacts so assistive technologies skip it. Set `HtmlToPdfOptions.Title` and `HtmlToPdfOptions.Language` (both required for an accessible PDF) to describe the document. Tagging requires the Chromium or Blink engine; accessibility quality depends on the source HTML (images need `alt`, headings used in order, proper table markup, etc.).

Since v26.5, tagged (PDF/UA-1) and PDF/A-3a conversions of real-world pages conform: the page's decorative paint (backgrounds, borders, separators) is marked as an artifact, content the engine left outside the structure is attached to the element it belongs to, a picture drawn twice is described once, and grouping elements hold their content the way the standard requires (checked with veraPDF on GitHub, the W3C home page and Wikipedia in English, Chinese, Arabic, Hebrew and Japanese). An HTML `<figure>` around one picture is tagged as that picture, with the picture's alternate text and the caption inside it; a figure around no picture or several is tagged as a group with its caption as a paragraph.

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

## ZUGFeRD / Factur-X electronic invoices (v26.4)

`PdfDocument.AddZugferdInvoice` turns a PDF/A-3 document into a hybrid electronic invoice: it embeds the invoice XML as a PDF/A-3 associated file, names it as the standards require (`factur-x.xml`, or `xrechnung.xml` for the XRechnung profile), and writes the ZUGFeRD / Factur-X identification metadata that invoice software looks for. The carrier must be a **PDF/A-3** document, so set `PdfStandard` to `PdfA3B`, `PdfA3U` or `PdfA3A` on the conversion first (`PdfA3A` also makes the visible invoice accessible, which the standards recommend).

- `ZugferdProfile` — the data profile of the XML: `Minimum`, `BasicWL`, `Basic`, `En16931`, `Extended`, `XRechnung`.
- `PdfAttachmentRelationship` — how the XML relates to the visible page: `Alternative` when both carry exactly the same invoice content (mandatory in Germany for Basic, EN 16931, Extended and XRechnung); `Data` when the page carries more than the XML (always the case for Minimum and Basic WL); `Source` when the page was generated from the XML.
- `ZugferdSchema` (optional 4th argument) — `FacturX10` (default, current) or `Zugferd20` (deprecated; only for recipients that still require it).
- Overloads take the XML as a `Stream` or as a file path.

Electronic invoices are a feature of the **commercial** library (not the Community Edition).

```csharp
using System.IO;
using SelectPdf;

HtmlToPdf converter = new HtmlToPdf();

// the invoice standards require a PDF/A-3 carrier
converter.Options.PdfStandard = PdfStandard.PdfA3B;
PdfDocument doc = converter.ConvertUrl("https://example.com/invoice");

using (FileStream xml = File.OpenRead("factur-x.xml"))
{
    doc.AddZugferdInvoice(xml, ZugferdProfile.En16931,
        PdfAttachmentRelationship.Alternative);
}

doc.Save("invoice.pdf");
doc.Close();
```

```vbnet
Imports System.IO
Imports SelectPdf

Dim converter As New HtmlToPdf()

' the invoice standards require a PDF/A-3 carrier
converter.Options.PdfStandard = PdfStandard.PdfA3B
Dim doc As PdfDocument = converter.ConvertUrl("https://example.com/invoice")

Using xml As FileStream = File.OpenRead("factur-x.xml")
    doc.AddZugferdInvoice(xml, ZugferdProfile.En16931, _
        PdfAttachmentRelationship.Alternative)
End Using

doc.Save("invoice.pdf")
doc.Close()
```

## Custom XMP metadata (v26.4)

A PDF/A document may only carry metadata from schemas the standard predefines, unless the schema itself is described inside the XMP packet. `PdfDocument.AddXmpExtensionSchema(prefix, namespaceUri, schemaName, properties)` writes both halves, the schema declaration and the property values, so the document stays PDF/A conformant. `properties` is an `IDictionary<string, string>` mapping each property name to its value; each is declared as an external text property. For invoices use `AddZugferdInvoice`, which writes the required schema itself. Commercial library only.

```csharp
using System.Collections.Generic;
using SelectPdf;

HtmlToPdf converter = new HtmlToPdf();
converter.Options.PdfStandard = PdfStandard.PdfA3B;
PdfDocument doc = converter.ConvertUrl("https://example.com/report");

doc.AddXmpExtensionSchema("acme", "https://example.com/ns/acme/1.0/", "Acme document metadata",
    new Dictionary<string, string>
    {
        { "DocumentId", "R-2026-0042" },
        { "Department", "Finance" }
    });

doc.Save("report.pdf");
doc.Close();
```

```vbnet
Imports System.Collections.Generic
Imports SelectPdf

Dim converter As New HtmlToPdf()
converter.Options.PdfStandard = PdfStandard.PdfA3B
Dim doc As PdfDocument = converter.ConvertUrl("https://example.com/report")

Dim properties As New Dictionary(Of String, String)()
properties.Add("DocumentId", "R-2026-0042")
properties.Add("Department", "Finance")
doc.AddXmpExtensionSchema("acme", "https://example.com/ns/acme/1.0/", "Acme document metadata", properties)

doc.Save("report.pdf")
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
