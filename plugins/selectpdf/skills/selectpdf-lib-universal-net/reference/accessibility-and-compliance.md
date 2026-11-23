# Accessible PDF (PDF/UA-1, PDF/UA-2), PDF/A and electronic invoices

Converted from the shipped SelectPdf.Universal v26.6 help topics. C# and VB.NET samples are the official ones.

## Accessible PDF and PDF/A

SelectPdf can generate **tagged (accessible) PDF** documents that declare the **PDF/UA** accessibility standard (PDF/UA-1 or PDF/UA-2), and **PDF/A** documents suitable for long term archiving, from PDF/A-1 up to PDF/A-4.

A tagged PDF carries a logical structure tree describing the meaning of the content - headings, paragraphs, lists, tables, figures with alternate text, links and the reading order. Assistive technologies such as screen readers use this structure to present the document to users with disabilities. Decorative content (backgrounds, rules, watermarks) is marked as an artifact so it is skipped by assistive technologies.

> **Note:** Tagged (accessible) PDF and PDF/A-3 generation are available only in the full commercial SelectPdf library. They are not available in the free SelectPdf community edition.

> **Note:** Tagged (PDF/UA) output and PDF/A level A are produced by the Chromium rendering engine, whether it runs on the application's machine or in a remote engine, so no engine selection is required. See "The Chromium engine" (installation-and-deployment.md) and "The remote engine" (installation-and-deployment.md).

### Tagging the document, and declaring a standard

These are two different things, and both properties exist:

- `PdfDocument.Tagged` (and `HtmlToPdfOptions.Tagged`) builds the logical structure tree. The document is accessible to a screen reader, but it makes no formal claim about itself.
- `PdfDocument.AccessibilityStandard` (and `HtmlToPdfOptions.AccessibilityStandard`) **declares** conformance with `PdfUAStandard.UA1` or `PdfUAStandard.UA2`: it turns tagging on and writes the PDF/UA identifier that validators and assistive software look for. `UA2` also writes the document as PDF 2.0, which that standard is defined on.

Declare a standard when you want a document that says it is PDF/UA - which is what a compliance requirement usually means. A document that is merely tagged carries no identifier, deliberately: a claim that the file does not live up to is worse than no claim at all.

> **Important:** The claim is verified when the document is saved. Content drawn on a page **before** the document was tagged is never marked, so declaring a standard afterwards would produce a file that fails validation - the save refuses it instead, naming the page. Set `AccessibilityStandard` (or `Tagged`) first, then add pages and elements.

Both standards require a title and a language, so set `PdfDocument.Title` and `PdfDocument.Language` (or the matching `HtmlToPdfOptions` properties) as well.

### Generate a tagged (accessible) PDF from HTML

Set `HtmlToPdfOptions.AccessibilityStandard` to the standard the document should declare. The converter takes the document structure (headings, lists, tables, figures, links, reading order) directly from the rendering engine's accessible output, so a well authored HTML page produces a well structured PDF.

```csharp
HtmlToPdf converter = new HtmlToPdf();

// produce a document that declares PDF/UA-1 (tagging is enabled automatically)
converter.Options.AccessibilityStandard = PdfUAStandard.UA1;
converter.Options.Title = "Quarterly Report";
converter.Options.Language = "en-US";

PdfDocument doc = converter.ConvertUrl("https://www.example.com");
doc.Save("accessible.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

' produce a document that declares PDF/UA-1 (tagging is enabled automatically)
converter.Options.AccessibilityStandard = PdfUAStandard.UA1
converter.Options.Title = "Quarterly Report"
converter.Options.Language = "en-US"

Dim doc As PdfDocument = converter.ConvertUrl("https://www.example.com")
doc.Save("accessible.pdf")
doc.Close()
```

Use `PdfUAStandard.UA2` for the newer standard (ISO 14289-2), which is written as a PDF 2.0 document. Internal links and outline entries then also carry a structure destination, so a reader following one lands at the right place in the reading order and not merely on the right page.

The quality of the resulting accessibility depends on the source HTML: images should have an `alt` attribute, headings should be used in order, tables should use proper table markup, and so on.

### Generate a PDF/A document from HTML

Set `HtmlToPdfOptions.PdfStandard` to the desired `PdfStandard` value. Archiving and accessibility are separate standards, so the two properties can be combined.

```csharp
HtmlToPdf converter = new HtmlToPdf();

// PDF/A-3A is archival AND accessible (tagging is enabled automatically)
converter.Options.PdfStandard = PdfStandard.PdfA3A;
converter.Options.AccessibilityStandard = PdfUAStandard.UA1;
converter.Options.Title = "Invoice 2026-0042";
converter.Options.Language = "en-US";

PdfDocument doc = converter.ConvertUrl("https://www.example.com/invoice");
doc.Save("invoice-pdfa3a.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

' PDF/A-3A is archival AND accessible (tagging is enabled automatically)
converter.Options.PdfStandard = PdfStandard.PdfA3A
converter.Options.AccessibilityStandard = PdfUAStandard.UA1
converter.Options.Title = "Invoice 2026-0042"
converter.Options.Language = "en-US"

Dim doc As PdfDocument = converter.ConvertUrl("https://www.example.com/invoice")
doc.Save("invoice-pdfa3a.pdf")
doc.Close()
```

### Create a tagged PDF from scratch

When building a PDF document with the SelectPdf creation API, declare the standard on the `PdfDocument` and assign a structure type to each page element through its `PdfPageElement.TagType` property. Use `PdfPageElement.AlternateText` to provide a textual description for figures, and mark purely decorative elements with `PdfPageElement.Artifact`.

In a tagged document every content element you add to a page **must** set either `TagType` (real content) or `Artifact = true` (decorative). Adding an element with neither raises an error rather than silently producing an inaccessible document - an untagged element would otherwise inherit the structure type of the element drawn before it.

> **Note:** Tagged and PDF/A documents require that every font is embedded. Add fonts created from a `PdfSystemFont` (or from a TrueType/OpenType file) - these are always embedded automatically (the non-embedded standard base fonts cannot be used).

```csharp
// create an accessible PDF/A-3A document
PdfDocument doc = new PdfDocument(PdfStandard.PdfA3A);
doc.AccessibilityStandard = PdfUAStandard.UA1;
doc.Language = "en-US";
doc.Title = "Accessible Document";

PdfPage page = doc.AddPage();

// embedded fonts are required for tagged / PDF/A output
PdfFont headingFont = doc.Fonts.Add(
    new PdfSystemFont("Arial", 16f, PdfFontStyle.Bold));
PdfFont bodyFont = doc.Fonts.Add(new PdfSystemFont("Arial", 11f));

// a heading
PdfTextElement heading = new PdfTextElement(10, 10, "Annual Report", headingFont);
heading.TagType = PdfTagType.Heading1;
page.Add(heading);

// a paragraph
PdfTextElement para = new PdfTextElement(10, 40, 480,
    "This document is tagged for accessibility.", bodyFont);
para.TagType = PdfTagType.Paragraph;
page.Add(para);

// a figure with alternate text
PdfImageElement figure = new PdfImageElement(10, 90, "chart.png");
figure.TagType = PdfTagType.Figure;
figure.AlternateText = "Sales grew 20% year over year";
page.Add(figure);

// a decorative rule (excluded from the structure)
PdfLineElement rule = new PdfLineElement(10, 80, 480, 80);
rule.Artifact = true;
page.Add(rule);

doc.Save("created-accessible.pdf");
doc.Close();
```

```vbnet
' create an accessible PDF/A-3A document
Dim doc As New PdfDocument(PdfStandard.PdfA3A)
doc.AccessibilityStandard = PdfUAStandard.UA1
doc.Language = "en-US"
doc.Title = "Accessible Document"

Dim page As PdfPage = doc.AddPage()

' embedded fonts are required for tagged / PDF/A output
Dim headingFont As PdfFont = doc.Fonts.Add(
    New PdfSystemFont("Arial", 16F, PdfFontStyle.Bold))
Dim bodyFont As PdfFont = doc.Fonts.Add(New PdfSystemFont("Arial", 11F))

' a heading
Dim heading As New PdfTextElement(10, 10, "Annual Report", headingFont)
heading.TagType = PdfTagType.Heading1
page.Add(heading)

' a paragraph
Dim para As New PdfTextElement(10, 40, 480,
    "This document is tagged for accessibility.", bodyFont)
para.TagType = PdfTagType.Paragraph
page.Add(para)

' a figure with alternate text
Dim figure As New PdfImageElement(10, 90, "chart.png")
figure.TagType = PdfTagType.Figure
figure.AlternateText = "Sales grew 20% year over year"
page.Add(figure)

' a decorative rule (excluded from the structure)
Dim rule As New PdfLineElement(10, 80, 480, 80)
rule.Artifact = True
page.Add(rule)

doc.Save("created-accessible.pdf")
doc.Close()
```

### Tagging tables and lists

Tables and lists need a nested structure (a table contains rows, a row contains header and data cells; a list contains list items). Open a container with `PdfDocument.BeginTag(PdfTagType)` and close it with `PdfDocument.EndTag`. Elements added, and further containers opened, before the matching `EndTag` become children of that container.

```csharp
doc.BeginTag(PdfTagType.Table);
    doc.BeginTag(PdfTagType.TableRow);
        page.Add(new PdfTextElement(10, 40, "Region", bodyFont)
            { TagType = PdfTagType.TableHeader });
        page.Add(new PdfTextElement(80, 40, "Sales", bodyFont)
            { TagType = PdfTagType.TableHeader });
    doc.EndTag();
    doc.BeginTag(PdfTagType.TableRow);
        page.Add(new PdfTextElement(10, 60, "North", bodyFont)
            { TagType = PdfTagType.TableDataCell });
        page.Add(new PdfTextElement(80, 60, "100", bodyFont)
            { TagType = PdfTagType.TableDataCell });
    doc.EndTag();
doc.EndTag();
```

```vbnet
doc.BeginTag(PdfTagType.Table)
    doc.BeginTag(PdfTagType.TableRow)
        Dim h1 As New PdfTextElement(10, 40, "Region", bodyFont)
        h1.TagType = PdfTagType.TableHeader
        page.Add(h1)
        Dim h2 As New PdfTextElement(80, 40, "Sales", bodyFont)
        h2.TagType = PdfTagType.TableHeader
        page.Add(h2)
    doc.EndTag()
    doc.BeginTag(PdfTagType.TableRow)
        Dim d1 As New PdfTextElement(10, 60, "North", bodyFont)
        d1.TagType = PdfTagType.TableDataCell
        page.Add(d1)
        Dim d2 As New PdfTextElement(80, 60, "100", bodyFont)
        d2.TagType = PdfTagType.TableDataCell
        page.Add(d2)
    doc.EndTag()
doc.EndTag()
```

### Supported standards

The `PdfStandard` enumeration selects the archiving standard of the generated document:

- `PdfA` / `PdfA1A` - PDF/A-1 at level B and level A (accessible: includes the tagged logical structure).
- `PdfA2B` / `PdfA2U` / `PdfA2A` - PDF/A-2 at level B, U (all text has Unicode mapping) and A.
- `PdfA3B` / `PdfA3U` / `PdfA3A` - PDF/A-3 at level B (visual reproduction), U (all text has Unicode mapping) and A (accessible: includes the tagged logical structure). PDF/A-3 is the level that may carry embedded files, which is what hybrid electronic invoices are built on.
- `PdfA4` - PDF/A-4, defined on PDF 2.0, plus the two variants `PdfA4E` (engineering: 3D and rich media) and `PdfA4F` (allows arbitrary embedded files).
- `PdfX` - PDF/X-1a:2001, for print production.

> **Note:** **Which archiving levels combine with which accessibility standard.** PDF/UA-2 is defined on PDF 2.0, so it combines only with PDF/A-4 (`PdfA4`, `PdfA4E`, `PdfA4F`). PDF/UA-1 is defined on PDF 1.7, so it combines with PDF/A-1a, PDF/A-2 and PDF/A-3 (`PdfA1A`, `PdfA2B`, `PdfA2U`, `PdfA2A`, `PdfA3B`, `PdfA3U`, `PdfA3A`) and with PDF/X-1a, but not with PDF/A-4. PDF/A-1b (`PdfA`) combines with neither: producing a PDF/A-1b file rebuilds the document in a way that does not keep its logical structure; use PDF/A-1a, which keeps it, for an accessible PDF/A-1 file. The A levels (`PdfA1A`, `PdfA2A`, `PdfA3A`) turn tagging on for a conversion, and a document built from code at one of them needs `Tagged` set. Asking for a combination that cannot be produced raises an error naming the levels that can be used instead, rather than producing a file that conforms to neither standard.

> **Note:** PDF/A does not permit certain interactive features - sound, movie and screen annotations, and link actions such as launching an external file or running JavaScript. PDF/UA-2 likewise does not permit sound annotations, which PDF 2.0 deprecates. Adding one of these raises an error that names the unsupported feature, rather than producing a non-conformant file.

> **Note:** **PDF/X-1a output is CMYK.** PDF/X-1a is a blind exchange standard for print: it permits DeviceCMYK, DeviceGray and spot colours, and does not permit DeviceRGB. A document produced at `PdfX` therefore has its colours converted to CMYK - text, vector graphics, images and gradients alike - and carries no transparency, which PDF 1.3 has no notion of. Translucent content is painted opaque rather than flattened. If you need the RGB colours preserved exactly, choose a PDF/A level instead; PDF/X exists for the printer's colour model, not for the screen's.

### Embedding files in a PDF/A-3 document

PDF/A-3 (and PDF/A-4F) may carry arbitrary embedded files alongside the visible document - the source spreadsheet a report was produced from, a machine readable copy of an invoice, a dataset. Embed one with `PdfDocument.AddAssociatedFile(Stream, string, PdfAttachmentRelationship, string, string)`, which records both the file and how it relates to the document.

```csharp
// PDF/A-3B is the archival level that allows embedded files
HtmlToPdf converter = new HtmlToPdf();
converter.Options.PdfStandard = PdfStandard.PdfA3B;
PdfDocument doc = converter.ConvertUrl("https://www.example.com/report");

// embed the data the report was produced from
using (FileStream data = File.OpenRead("figures.csv"))
{
    doc.AddAssociatedFile(data, "figures.csv",
        PdfAttachmentRelationship.Source, "text/csv", "Source data");
}

doc.Save("report-with-data.pdf");
doc.Close();
```

```vbnet
' PDF/A-3B is the archival level that allows embedded files
Dim converter As New HtmlToPdf()
converter.Options.PdfStandard = PdfStandard.PdfA3B
Dim doc As PdfDocument = converter.ConvertUrl("https://www.example.com/report")

' embed the data the report was produced from
Using data As FileStream = File.OpenRead("figures.csv")
    doc.AddAssociatedFile(data, "figures.csv",
        PdfAttachmentRelationship.Source, "text/csv", "Source data")
End Using

doc.Save("report-with-data.pdf")
doc.Close()
```

The `PdfAttachmentRelationship` value says what the file is: `Source` (the document was generated from it), `Data` (data the document presents), `Alternative` (the same content in another form), `Supplement` or `Unspecified`. The stream is read when the file is added, so you are free to close it as soon as the call returns.

For ZUGFeRD / Factur-X invoices, use `PdfDocument.AddZugferdInvoice(Stream, ZugferdProfile, PdfAttachmentRelationship)` instead - it embeds the xml and writes the invoice metadata in one call. See "ZUGFeRD / Factur-X electronic invoices" (accessibility-and-compliance.md).

### What a document that declares a standard will not do

A few shapes cannot produce a conformant file, and are refused at the point of use with a message naming the alternative rather than silently producing a document that fails validation:

- **Content added to an existing (loaded) document.** A document loaded from a file is not re-tagged, and content added to one carries no structure. A page added to a loaded document that already carries a structure tree does join it; a document that arrived untagged cannot declare a standard.
- **A `PdfHtmlElement` in a tagged document.** On a page, the element carries the structure of the html it renders - its headings, paragraphs, lists and tables join the document's reading order with nothing to set. It is refused when it is given a single `PdfPageElement.TagType`, because the rendered html is not one element: clear the tag type, or set `Artifact = true` to declare the whole region decorative. In a header, footer or stamp template it is refused unless it is decorative (`Artifact = true`): a template repeats on every page, so its html has no single place in the reading order.
- **Declaring a standard after drawing.** See the note above - the save verifies the claim and refuses a document whose content was never marked.

Appending one document to another carries the appended document's structure across, so a tagged document built from several parts stays tagged - see "Merging PDFs" (edit-merge-split-stamp.md).

## Alternate text for images in existing PDFs

SelectPdf PDF Library for .NET can fix the alternative text of the images in an existing tagged PDF document: add a description where one is missing, replace a bad one (a file name, "image1"), or mark an image that is only decoration as decorative. Missing alternative text is the most common reason a PDF fails PDF/UA (ISO 14289-1, checkpoint 7.3), and this turns such a file into a passing one without generating it again.

The work is done with `PdfAccessibilityManager`:

- `PdfAccessibilityManager.Load(string)` - loads the tagged PDF document, password protected or not, from a file, Stream, byte array or `PdfDocument` object.
- `PdfAccessibilityManager.Figures` - the figures of the document, in reading order: its Figure and Formula elements, including custom element types the document maps to them.
- `PdfAccessibilityManager.MissingAlternateText` - the figures that still have no description: neither alternative nor actual text, or only a placeholder such as a file name ("chart1.png"), a numbered name ("image1", "Picture 3") or a GUID. Decorative figures are not listed.
- `PdfAccessibilityManager.Save(string)` - saves the document with the changes to a file, Stream or byte array, and `PdfAccessibilityManager.GetDocument` returns it as a `PdfDocument` object, to continue working on it.

### A figure

Each `PdfFigure` says where it is and what describes it:

- `PdfFigure.PageIndex` and `PdfFigure.Bounds` - the page it starts on and its box on it, in points from the top-left corner of the page as displayed.
- `PdfFigure.Type` - Figure, or Formula, which needs a description too.
- `PdfFigure.AlternateText` - the description a screen reader reads in place of the image.
- `PdfFigure.ActualText` - the text the figure shows, when it is a picture of text: a formula, a scanned word, a logo with a name in it.
- `PdfFigure.IsDecorative` - set it for an image that carries no information (a border, a background flourish). When saved, the figure leaves the structure tree and its content is marked as an artifact: the page looks the same, and screen readers skip it.
- `PdfFigure.GetImage(int)` - renders the figure's area of its page as a PNG image (150 dpi by default), to show a person who writes the description, or to send to a service that describes images.

Setting a text to null or an empty string removes it.

### Code sample

Describe every figure that has no description, and mark a decorative one:

```csharp
using (PdfAccessibilityManager manager = new PdfAccessibilityManager())
{
    manager.Load("report.pdf");

    foreach (PdfFigure figure in manager.MissingAlternateText)
    {
        // show the image to a person, or to an image-description service
        byte[] png = figure.GetImage(150);
        figure.AlternateText = Describe(png);
    }

    manager.Figures[3].IsDecorative = true;      // a border: screen readers skip it

    manager.Save("report-fixed.pdf");
}
```

```vbnet
Using manager As New PdfAccessibilityManager()
    manager.Load("report.pdf")

    For Each figure As PdfFigure In manager.MissingAlternateText
        ' show the image to a person, or to an image-description service
        Dim png As Byte() = figure.GetImage(150)
        figure.AlternateText = Describe(png)
    Next

    manager.Figures(3).IsDecorative = True

    manager.Save("report-fixed.pdf")
End Using
```

### Tagged documents, signatures and PDF/UA

- **Tagged documents only.** An untagged document has no structure element to carry a description, and alternative text alone would not make it accessible, so Load refuses it with a `PdfDocumentException` that says so. To make new documents accessible from the start, see "Accessible PDF and PDF/A" (accessibility-and-compliance.md).
- Setting a text writes the element's /Alt (or /ActualText) entry and nothing else; the pages are not changed.
- A signed document is appended to, so its signatures still verify after the change.
- A document that declares PDF/UA is checked again when it is saved: a change that would leave some of its content outside the structure tree is refused.
- Documents the library creates with `PdfDocument.Tagged` write an image's `PdfPageElement.AlternateText` as its description, and an image given none gets no description (version 26.6 and later; earlier versions wrote a GUID in its place). Such a figure is listed by `PdfAccessibilityManager.MissingAlternateText`.

## ZUGFeRD / Factur-X electronic invoices

A **hybrid electronic invoice** carries the same invoice twice: the page a person reads, and the xml a machine reads, embedded inside the same pdf. **ZUGFeRD** (Germany) and **Factur-X** (France) are the same standard under two names, and both are what most European e-invoicing rules ask for.

SelectPdf turns a document into such an invoice with a single call: `PdfDocument.AddZugferdInvoice(Stream, ZugferdProfile, PdfAttachmentRelationship)` embeds the invoice xml as an associated file and writes the metadata that invoice software looks for. The name of the embedded file is derived from the profile rather than taken from you - the standards fix it and recipients look it up by name.

> **Note:** The carrier must be a **PDF/A-3** document (`PdfStandard.PdfA3B`, `PdfStandard.PdfA3U` or `PdfStandard.PdfA3A`), which the invoice standards require. A document that is not PDF/A-3 is refused with a message naming the fix, rather than producing a file no invoice software will accept.

> **Note:** Electronic invoices are available only in the full commercial SelectPdf library. They are not available in the free SelectPdf community edition.

> **Important:** SelectPdf embeds and identifies the invoice xml you supply. Producing that xml - and making sure its content matches the visible invoice - is your application's job; SelectPdf does not generate or validate it.

### Create a hybrid invoice from an HTML invoice

Convert the invoice page as PDF/A-3, then attach the xml to the converted document before saving it. Use `PdfStandard.PdfA3A` together with `HtmlToPdfOptions.AccessibilityStandard` if the visible invoice should also be accessible, which the standards recommend.

```csharp
// the carrier: a PDF/A-3 document produced from the invoice page
HtmlToPdf converter = new HtmlToPdf();
converter.Options.PdfStandard = PdfStandard.PdfA3B;

PdfDocument doc =
    converter.ConvertUrl("https://www.example.com/invoice/2026-001");

// embed the invoice xml and mark the document as an electronic invoice
using (FileStream xml = File.OpenRead("factur-x.xml"))
{
    doc.AddZugferdInvoice(xml, ZugferdProfile.En16931,
        PdfAttachmentRelationship.Alternative);
}

// save pdf document
doc.Save("invoice-2026-001.pdf");

// close pdf document
doc.Close();
```

```vbnet
' the carrier: a PDF/A-3 document produced from the invoice page
Dim converter As New HtmlToPdf()
converter.Options.PdfStandard = PdfStandard.PdfA3B

Dim doc As PdfDocument =
    converter.ConvertUrl("https://www.example.com/invoice/2026-001")

' embed the invoice xml and mark the document as an electronic invoice
Using xml As FileStream = File.OpenRead("factur-x.xml")
    doc.AddZugferdInvoice(xml, ZugferdProfile.En16931,
        PdfAttachmentRelationship.Alternative)
End Using

' save pdf document
doc.Save("invoice-2026-001.pdf")

' close pdf document
doc.Close()
```

There is an overload that takes the path of the xml file instead of a stream. Either way the xml is read immediately, so the file or stream is yours to release as soon as the call returns.

### Choosing the profile

The `ZugferdProfile` value says how much of the EN 16931 semantic model the embedded xml carries. It is recorded in the invoice metadata as the conformance level, so it must describe the xml you actually embed.

| Profile | Meaning |
|---|---|
| Minimum | Accounting information only ("Buchungshilfe"). Not a complete invoice. |
| BasicWL | Header and footer data without invoice lines ("without lines"). |
| Basic | A subset of EN 16931 covering simple invoices, with lines. |
| En16931 | The full European semantic standard (formerly COMFORT). The usual choice. |
| Extended | EN 16931 plus additional business terms for more complex processes. |
| XRechnung | The German public-sector reference profile. The embedded file is named `xrechnung.xml` instead of `factur-x.xml`. |

### How the xml relates to the visible invoice

The `PdfAttachmentRelationship` value records that relationship, and recipients rely on it:

- `Alternative` - the xml and the visible page carry exactly the same invoice content. This is mandatory in Germany for the BASIC, EN 16931, EXTENDED and XRECHNUNG profiles, and is the usual value.
- `Data` - the visible page carries more than the xml does. Always the case for the MINIMUM and BASIC WL profiles, which are not complete invoices.
- `Source` - the visible page was generated from the xml.

### Turn an existing pdf into an invoice

An invoice that already exists as a pdf can be converted to PDF/A-3 first with `PdfDocument.ConvertToPdfA(PdfStandard)` and then given the xml. A document that is already PDF/A-3 needs no conversion.

```csharp
// load the existing invoice and make it an archival PDF/A-3 document
PdfDocument doc = new PdfDocument("invoice.pdf");
doc.ConvertToPdfA(PdfStandard.PdfA3B);

// embed the invoice xml and mark the document as an electronic invoice
doc.AddZugferdInvoice("factur-x.xml", ZugferdProfile.En16931,
    PdfAttachmentRelationship.Alternative);

doc.Save("invoice-hybrid.pdf");
doc.Close();
```

```vbnet
' load the existing invoice and make it an archival PDF/A-3 document
Dim doc As New PdfDocument("invoice.pdf")
doc.ConvertToPdfA(PdfStandard.PdfA3B)

' embed the invoice xml and mark the document as an electronic invoice
doc.AddZugferdInvoice("factur-x.xml", ZugferdProfile.En16931,
    PdfAttachmentRelationship.Alternative)

doc.Save("invoice-hybrid.pdf")
doc.Close()
```

### The deprecated ZUGFeRD 2.0 metadata

Some recipients still require the older ZUGFeRD 2.0 metadata schema. An overload taking `ZugferdSchema` writes that instead of the current Factur-X 1.0 schema; the embedded file is then named `zugferd-invoice.xml`. Use it only when a recipient asks for it.

```csharp
doc.AddZugferdInvoice(xml, ZugferdProfile.Basic,
    PdfAttachmentRelationship.Alternative, ZugferdSchema.Zugferd20);
```

```vbnet
doc.AddZugferdInvoice(xml, ZugferdProfile.Basic,
    PdfAttachmentRelationship.Alternative, ZugferdSchema.Zugferd20)
```

### Custom metadata of your own

A PDF/A document may only carry metadata from the schemas the standard predefines, unless the schema itself is described inside the document. That is the mechanism the invoice metadata is built on, and it is available for metadata a recipient's system needs to find in your documents: `PdfDocument.AddXmpExtensionSchema(string, string, string, IDictionary<string, string>)` declares the schema and writes the values of its properties, so the document stays conformant.

```csharp
Dictionary<string, string> properties = new Dictionary<string, string>();
properties["OrderNumber"] = "PO-4711";
properties["Department"] = "Accounts payable";

doc.AddXmpExtensionSchema("acme", "urn:acme:pdfa:custom:1p0#",
    "ACME custom schema", properties);
```

```vbnet
Dim properties As New Dictionary(Of String, String)()
properties("OrderNumber") = "PO-4711"
properties("Department") = "Accounts payable"

doc.AddXmpExtensionSchema("acme", "urn:acme:pdfa:custom:1p0#",
    "ACME custom schema", properties)
```

## Validating PDF/A, PDF/UA and PDF/X

SelectPdf PDF Library for .NET can check whether an existing PDF document conforms to a standard: PDF/A (archiving, parts 1 to 4), PDF/UA (accessibility, parts 1 and 2) and PDF/X (print exchange: PDF/X-1a, PDF/X-3 and PDF/X-4). Every rule of the standard is checked and each object that fails one is reported, with the rule, what it requires, why the object fails it and where the object is. The document is only read, never changed.

Validation is done with `PdfValidator`:

- `PdfValidator.Validate(byte[], string, PdfConformanceStandard, ValidationOptions)` - validates a document against a `PdfConformanceStandard` and returns a `ValidationReport`.
- `PdfValidator.ClaimedStandard(byte[], string)` - returns the standard a document says it conforms to in its metadata, or null when it claims none.

### The report

- `ValidationReport.IsCompliant` is true when every check passed and the validation was complete.
- `ValidationReport.Failures` lists the failed checks, each a `RuleFailure`: the rule (its specification, clause and test number), what the rule requires, why the object fails it, the object's place in the document as a path (`document/pages[3]/annots[0]`), its page and its object number. At most 100 failures are listed per rule by default; `ValidationReport.ChecksFailed` counts them all.
- `ValidationReport.Complete` is false when the validation stopped early - at a time or size limit, or on a part of the file that could not be read. Such a report is never compliant: a check that was not made is not a check that passed.

The limits are set with `ValidationOptions`: the failures listed per rule and in all, and how long the validation may run, reading the file included (30 seconds by default). The memory a validation holds is limited too, to about 16 GB (1 GB in a 32-bit process, and no more than half the memory the process may use), so that a very large or hostile file ends as an incomplete report rather than taking the process's memory.

### The rules

PDF/A and PDF/UA are checked with the validation profiles of the veraPDF project, the reference for these standards; the results agree with veraPDF's on its test corpora. PDF/X is checked with rules written from the public descriptions of ISO 15930. The version of the rules is in `ValidationReport.ProfilesVersion`.

### Example

```csharp
byte[] pdf = File.ReadAllBytes("document.pdf");

ValidationReport report = PdfValidator.Validate(pdf, null,
    PdfConformanceStandard.PdfA2B);

if (report.IsCompliant)
{
    Console.WriteLine("The document conforms to PDF/A-2b.");
}
else
{
    if (!report.Complete)
        Console.WriteLine("The validation did not complete.");
    foreach (RuleFailure failure in report.Failures)
        Console.WriteLine(failure.Rule + ": " + failure.Message
            + " (" + failure.Context + ")");
}
```

```vbnet
Dim pdf As Byte() = File.ReadAllBytes("document.pdf")

Dim report As ValidationReport = PdfValidator.Validate(pdf, Nothing,
    PdfConformanceStandard.PdfA2B)

If report.IsCompliant Then
    Console.WriteLine("The document conforms to PDF/A-2b.")
Else
    If Not report.Complete Then
        Console.WriteLine("The validation did not complete.")
    End If
    For Each failure As RuleFailure In report.Failures
        Console.WriteLine(failure.Rule.ToString() & ": " & failure.Message _
            & " (" & failure.Context & ")")
    Next
End If
```

To check a document against the standard it claims:

```csharp
PdfConformanceStandard? claimed = PdfValidator.ClaimedStandard(pdf, null);
if (claimed != null)
{
    ValidationReport report = PdfValidator.Validate(pdf, null, claimed.Value);
    Console.WriteLine(claimed + ": " + (report.IsCompliant ? "yes" : "no"));
}
```

```vbnet
Dim claimed As PdfConformanceStandard? = PdfValidator.ClaimedStandard(pdf, Nothing)
If claimed.HasValue Then
    Dim report As ValidationReport =
        PdfValidator.Validate(pdf, Nothing, claimed.Value)
    Console.WriteLine(claimed.Value.ToString() & ": " _
        & If(report.IsCompliant, "yes", "no"))
End If
```

## Fixing documents for PDF/A, PDF/UA and PDF/X (preflight)

SelectPdf PDF Library for .NET can fix an existing PDF document so that it conforms to a standard: PDF/A, PDF/UA or PDF/X. The document is validated first; one that conforms already is returned as it is. Otherwise the fixes for what it fails are applied, and the result is validated again, so you see what was fixed and what is left.

Fixing is done with `PdfPreflight.Fix(byte[], string, PdfConformanceStandard, PreflightOptions)`, which returns a `PreflightResult`: the fixed document, the fixes applied (`PreflightFix`: what each did and to how many objects) and the `ValidationReport` of the fixed document. A digitally signed document is refused: every fix rewrites the file, which the signature would no longer cover. Fix the document before it is signed.

The whole fix - the validation before, the fixes and the validation after - runs within the timeout of `PreflightOptions.Validation` (30 seconds by default) and under the memory limit a validation has. Past the timeout `Fix` throws `TimeoutException`; raise `ValidationOptions.Timeout` for very large documents.

### PDF/A

The document is converted to the PDF/A level you ask for, any part from PDF/A-1 to PDF/A-4. Before the conversion, actions the level forbids (launching applications, playing sounds and movies, and from PDF/A-2 hiding fields and the like) are removed, fonts are embedded, the text of glyphs the document does not map to Unicode is recovered where the font allows it, and embedded files are given the names, type and relationship the level requires. PDF/A-3 and PDF/A-4f keep embedded files; for the other levels, embedded files the level does not admit are removed only when `PreflightOptions.RemoveAttachmentsAndJavaScript` is set - otherwise they are kept and reported.

### PDF/UA

A document without tags is tagged (headings, paragraphs, lists, tables, figures, links and form fields are recognized on the page), and a document whose tags leave content outside them is tagged again. Then the language is set from `PreflightOptions.Language` when the document has none - it is never guessed - and a missing title is taken from the first heading. Annotations and form fields are tagged, links are described, heading levels that skip a level are renumbered, and fonts are embedded and mapped to Unicode. A PDF/A claim the standard contradicts (PDF/A-1 to 3 under PDF/UA-2, which is a PDF 2.0 standard, or PDF/A-4 under PDF/UA-1) is removed and reported; a PDF/A claim that can hold is kept valid.

What needs a person is still reported after the fixes: alternate text for figures, the headers of an irregular table, the label of a form field. Use the tags and alternate text APIs to supply them, then validate again.

### PDF/X

Encryption is removed; the identification, dates and output intent are written; every page gets a TrimBox; colour is converted to CMYK - through the output profile you give (`PreflightOptions.OutputProfile`, an ICC printer profile), or with the device formula without one - or, under an RGB or Gray output intent, given the colour spaces that describe it. Transparency, hidden layers, transfer functions and compressions the level forbids are removed, and fonts are embedded from the installed fonts. Annotations in the printed area are removed only when `PreflightOptions.RemoveAnnotations` is set (what they print is drawn into the page first), and embedded files and JavaScript only when `PreflightOptions.RemoveAttachmentsAndJavaScript` is set.

### Example

```csharp
byte[] pdf = File.ReadAllBytes("report.pdf");

PreflightResult result = PdfPreflight.Fix(pdf, null,
    PdfConformanceStandard.PdfUA1,
    new PreflightOptions(Language: "en-US"));

foreach (PreflightFix fix in result.Applied)
    Console.WriteLine(fix.Description + " (" + fix.Count + ")");

if (result.After.IsCompliant)
    File.WriteAllBytes("report-ua1.pdf", result.Pdf);
else
    foreach (RuleFailure failure in result.After.Failures)
        Console.WriteLine("Left: " + failure.Rule + " " + failure.Message);
```

```vbnet
Dim pdf As Byte() = File.ReadAllBytes("report.pdf")

Dim result As PreflightResult = PdfPreflight.Fix(pdf, Nothing,
    PdfConformanceStandard.PdfUA1,
    New PreflightOptions(Language:="en-US"))

For Each fix As PreflightFix In result.Applied
    Console.WriteLine(fix.Description & " (" & fix.Count & ")")
Next

If result.After.IsCompliant Then
    File.WriteAllBytes("report-ua1.pdf", result.Pdf)
Else
    For Each failure As RuleFailure In result.After.Failures
        Console.WriteLine("Left: " & failure.Rule.ToString() & " " & failure.Message)
    Next
End If
```

For print, with a printer profile:

```csharp
var options = new PreflightOptions(
    OutputProfile: File.ReadAllBytes("ISOcoated_v2_eci.icc"),
    RemoveAnnotations: true);
PreflightResult result = PdfPreflight.Fix(pdf, null,
    PdfConformanceStandard.PdfX4, options);
```

```vbnet
Dim options As New PreflightOptions(
    OutputProfile:=File.ReadAllBytes("ISOcoated_v2_eci.icc"),
    RemoveAnnotations:=True)
Dim result As PreflightResult = PdfPreflight.Fix(pdf, Nothing,
    PdfConformanceStandard.PdfX4, options)
```
