# Word to PDF

Converted from the shipped SelectPdf.Universal v26.5 help topics. C# and VB.NET samples are the official ones.

## The Word to PDF converter

Using the SelectPdf Word to Pdf Converter for .NET, a Word document can be converted to pdf with a few lines of code. The converter reads the formats Microsoft Word writes - docx, docm, dotx, dotm, doc, dot - plus rtf, plain text, WordML, and html and markdown through its own importers. The conversion runs in-process: no engine is launched.

> **Note:** The Word to Pdf converter is available only in the full commercial SelectPdf library. It is not available in the free SelectPdf community edition.

The `WordToPdf` class exposes `WordToPdf.ConvertFile(string)`, `WordToPdf.ConvertStream(Stream)` and `WordToPdf.ConvertBytes(byte[])`, each with an overload that names the `WordFormat` to read. Without one, the format is taken from the file extension, or detected from the content for a stream or a byte array (docx family, doc, rtf, WordML). Plain text, html and markdown have no signature and must be named when given as a stream.

The result is a `PdfDocument`, exactly as the html converter returns one: it can be modified further, saved to a file, a stream or a byte array, and must be closed. The `WordToPdf.Options`, `WordToPdf.Header` and `WordToPdf.Footer` properties mirror the html converter's, so a document setup written for `HtmlToPdf` applies here unchanged - page size and margins, header and footer bands, PDF/A and PDF/X standards, security, viewer preferences, document properties, compression, page ranges and single-page mode. The options are reviewed in "Word to PDF options" (word-to-pdf.md).

### Page setup: keep the Word pages, or reflow

A Word document has pages of its own, so `WordToPdfOptions.PageSetup` decides what the page size options mean:

- `WordPageSetup.Document` (the default) keeps every page the size and orientation the Word document gives it, mixed sizes included. The page size and orientation options are ignored; the margins and the header and footer bands are applied around the rendered page, which is scaled uniformly to fit inside them. With zero margins and no bands the page is stamped 1:1 - nothing is reflowed and no page break moves.
- `WordPageSetup.Options` reflows the text onto the page size, orientation and margins of the options, replacing the Word document's own margins. This is what "convert this docx to A4 landscape with one-inch margins" means.

### Sample Code

This sample converts a Word document, keeping its own pages, under a header band and page numbers, and saves it as PDF/A-2b.

```csharp
WordToPdf converter = new WordToPdf();

// Document keeps the Word page sizes;
// Options would reflow onto PdfPageSize / margins
converter.Options.PageSetup = WordPageSetup.Document;
converter.Options.PdfStandard = PdfStandard.PdfA2B;

// a text header band and a page-number footer
converter.Options.DisplayHeader = true;
converter.Header.Height = 40;
converter.Header.Add(new PdfTextSection(0, 12, 500, "Quarterly report",
    PdfFont.CreateStandardFont(PdfStandardFont.HelveticaBold, 11),
    new PdfColor(44, 62, 80)));
converter.Options.DisplayFooter = true;
converter.Footer.Height = 30;
converter.Footer.Add(new PdfTextSection(0, 10, 500,
    "Page {page_number} of {total_pages}",
    PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 9),
    new PdfColor(80, 80, 80)));

// the format is taken from the extension
PdfDocument doc = converter.ConvertFile("report.docx");

// the outline was built from the document's headings
int chapters = doc.Bookmarks.Count;

doc.Save("report.pdf");
doc.Close();
```

```vbnet
Dim converter As New WordToPdf()

' Document keeps the Word page sizes;
' Options would reflow onto PdfPageSize / margins
converter.Options.PageSetup = WordPageSetup.Document
converter.Options.PdfStandard = PdfStandard.PdfA2B

' a text header band and a page-number footer
converter.Options.DisplayHeader = True
converter.Header.Height = 40
converter.Header.Add(New PdfTextSection(0, 12, 500, "Quarterly report",
    PdfFont.CreateStandardFont(PdfStandardFont.HelveticaBold, 11),
    New PdfColor(44, 62, 80)))
converter.Options.DisplayFooter = True
converter.Footer.Height = 30
converter.Footer.Add(New PdfTextSection(0, 10, 500,
    "Page {page_number} of {total_pages}",
    PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 9),
    New PdfColor(80, 80, 80)))

' the format is taken from the extension
Dim doc As PdfDocument = converter.ConvertFile("report.docx")

' the outline was built from the document's headings
Dim chapters As Integer = doc.Bookmarks.Count

doc.Save("report.pdf")
doc.Close()
```

### Fonts

Every font the document uses is embedded in the pdf as a subset (or complete, with `WordToPdfOptions.EmbedCompleteFonts`). A font family the host does not have is served from the fonts the library bundles - the Liberation faces, metric-compatible with Arial, Times New Roman and Courier New - so the same document converts on a Windows server, a Linux container with no fonts installed, or a Mac. Scripts those faces do not cover (CJK, Arabic, Indic) need fonts installed on the host.

### Archiving standards and accessibility

Every PDF/A level and PDF/X-1a are produced the way the html converter produces them, through `WordToPdfOptions.PdfStandard`.

Accessible (tagged) output is declared the way it is on the html converter: `WordToPdfOptions.Tagged` builds the logical structure - the Word document's headings, paragraphs, lists, tables with their header cells, pictures, links, footnotes and text boxes - and `WordToPdfOptions.AccessibilityStandard` declares PDF/UA-1 or PDF/UA-2 (which is written as PDF 2.0). A declared document needs a `WordToPdfOptions.Title` and a `WordToPdfOptions.Language`. `PdfStandard.PdfA3A` turns tagging on by itself, and combines with PDF/UA-1; PDF/A-4 is the archiving level that combines with PDF/UA-2. Running headers and footers are marked as pagination artifacts, and the fonts of a header or footer band are embedded on a declared document, PDF/UA requiring it.

A picture without alternative text in the Word document is given the placeholder `Image`; give every picture a description in Word for a document that is accessible in substance and not only in form. A validator such as veraPDF is the way to check the produced document at the level it declares.

## Word to PDF options

`WordToPdfOptions`, reached through `WordToPdf.Options`, controls the conversion and the generated document. Most of it is the same surface as `HtmlToPdfOptions`, with the same defaults, so it is only listed here; the properties that belong to the Word engine are described.

### The options shared with the html converter

| Property | What it does |
|---|---|
| `WordToPdfOptions.PdfPageSize`, `WordToPdfOptions.PdfPageCustomSize`, `WordToPdfOptions.PdfPageOrientation` | The page of the generated document. Applied only under the `Options` page setup; under `Document` the Word document's own pages are kept. |
| `WordToPdfOptions.MarginLeft`, `MarginRight`, `MarginTop`, `MarginBottom` | Margins in points, zero by default. Applied under both page setups: they surround the rendered page (Document) or replace the Word margins (Options). |
| `WordToPdfOptions.DisplayHeader`, `WordToPdfOptions.DisplayFooter` | Show the bands built through `WordToPdf.Header` and `WordToPdf.Footer`. Text, image, html and Word sections all work; see "Word sections" (word-to-pdf.md). |
| `WordToPdfOptions.ExternalLinksEnabled`, `WordToPdfOptions.InternalLinksEnabled` | Carry the document's web links and its bookmark (internal) links as clickable pdf links. Both on by default. |
| `WordToPdfOptions.PageRanges` | Keep only the named rendered pages, e.g. `"1,3-5"`. A range that selects none of the rendered pages is refused. |
| `WordToPdfOptions.GenerateSinglePagePdf` | Stack every rendered page onto one tall pdf page. |
| `WordToPdfOptions.PdfStandard`, `WordToPdfOptions.ColorSpace` | PDF/A-1b to PDF/A-4 and PDF/X-1a, and the color space, produced as the html converter produces them. |
| `WordToPdfOptions.Tagged`, `WordToPdfOptions.AccessibilityStandard`, `WordToPdfOptions.Title`, `WordToPdfOptions.Language` | Tagged (accessible) output: the Word document's structure becomes the logical structure of the pdf, declared as PDF/UA-1 or PDF/UA-2 with the title and the language a declared document must carry. See the Standards section of the converter topic. |
| `WordToPdfOptions.SecurityOptions`, `WordToPdfOptions.ViewerPreferences`, `WordToPdfOptions.PdfDocumentInformation` | Passwords and permissions, how a viewer opens the document, and its title, author, subject and keywords. |
| `WordToPdfOptions.JpegCompressionEnabled`, `WordToPdfOptions.JpegCompressionLevel`, `WordToPdfOptions.PdfCompressionLevel` | Image and stream compression, with the html converter's defaults. |
| `WordToPdfOptions.ImagesMaxResolution` | The highest resolution, in pixels per inch, an image is kept at; images drawn at a higher resolution are resampled down to it. 0 (the default) keeps every image as it is. |
| `WordToPdfOptions.LFN` | A diagnostic log file written during the conversion. |

### The Word engine's own options

| Property | Default | What it does |
|---|---|---|
| `WordToPdfOptions.PageSetup` | `Document` | Whether the output pages keep the Word document's sizes (`Document`) or the text is reflowed onto the page size, orientation and margins of the options (`Options`). See "The Word to PDF converter" (word-to-pdf.md). |
| `WordToPdfOptions.Password` | none | The password of an encrypted Word document. |
| `WordToPdfOptions.Bookmarks` | `Headings` | What the pdf outline is made from: the document's headings (nested by level), its Word bookmarks, or nothing (`WordBookmarks`). |
| `WordToPdfOptions.UpdateFields` | `true` | Update the document's fields (dates, page references, formulas) before rendering. |
| `WordToPdfOptions.PreserveFormFields` | `false` | Keep the document's form fields as fillable pdf form fields instead of flattening them. |
| `WordToPdfOptions.EmbedCompleteFonts` | `false` | Embed the complete font programs rather than the subsets the document uses. |
| `WordToPdfOptions.AutoDetectComplexScript` | `false` | Detect and shape complex scripts (Arabic, Hebrew, Indic) even where the document does not mark them. |
| `WordToPdfOptions.OptimizeIdenticalImages` | `true` | Store identical images once. |

### The conversion result

After a conversion, `WordToPdf.ConversionResult` describes the document produced: `WordToPdfResult.PdfPageCount`, `WordToPdfResult.PdfPageSize`, `WordToPdfResult.PdfPageLastIndex`, `WordToPdfResult.PdfPageLastRectangle` and `WordToPdfResult.PdfPagesRectangles`, one rectangle per rendered page in the coordinates of the pdf page it landed on.

## Word documents as PDF elements

`PdfWordElement` draws a Word document into a region of a pdf page, the way `PdfHtmlElement` draws html there. The document is given as a file path or a stream, in any format the Word converter reads, and is rendered in-process at render time with every page set to the size of the region - the region is the paper. Its web links and bookmark links stay clickable, and its headings (or its Word bookmarks, per `PdfWordElement.Bookmarks`) become entries in the outline of the host document.

Coordinates and sizes are in the page's client-area coordinate system, relative to the top-left corner of the writable area inside the page margins. `PdfWordElement.Width` defaults to the client width minus X; `PdfWordElement.Height` decides how the content flows:

- A fixed height that fits its page draws the first rendered page, cut at the box.
- The default, -1, lets the content flow onto the pages that follow, using the document's next page when there is one and appending one when there is not - so two overflowing regions on one page share the pages after it.
- A fixed height taller than what is left of the page continues on the pages that follow, the same way, until that much height has been drawn.

The Word engine lays a document out on pages of one height, so a region that starts part-way down a page is laid out on pages of whichever height leaves the least blank space on any one page: the room left when most of a page is left (the pages after the first then all end that much early), otherwise the largest whole fraction of a page that fits the room (the first page ends early and the pages after it are whole). A region that starts at the top of a page fills every page.

> **Note:** `PdfWordElement` is available only in the full commercial SelectPdf library.

### Sample Code

A title drawn from code, then a Word document flowing from below it onto the pages that follow.

```csharp
PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();

PdfFont titleFont =
    PdfFont.CreateStandardFont(PdfStandardFont.HelveticaBold, 20);
page.Add(new PdfTextElement(0, 0, "Annex A - the attached report", titleFont,
    new PdfColor(44, 62, 80)));

// from 60pt down the page, continuing on the pages after it
PdfWordElement report = new PdfWordElement(0, 60, "report.docx");
PdfRenderingResult result = page.Add(report);

// where each rendered page landed, and the page the region ended on
SelectPdf.Universal.Drawing.RectangleF[] slices = report.PdfPagesRectangles;
int lastPage = result.PdfPageLastIndex;

doc.Save("annex.pdf");
doc.Close();
```

```vbnet
Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()

Dim titleFont As PdfFont = _
    PdfFont.CreateStandardFont(PdfStandardFont.HelveticaBold, 20)
page.Add(New PdfTextElement(0, 0, "Annex A - the attached report", titleFont, _
    New PdfColor(44, 62, 80)))

' from 60pt down the page, continuing on the pages after it
Dim report As New PdfWordElement(0, 60, "report.docx")
Dim result As PdfRenderingResult = page.Add(report)

' where each rendered page landed, and the page the region ended on
Dim slices As SelectPdf.Universal.Drawing.RectangleF() = report.PdfPagesRectangles
Dim lastPage As Integer = result.PdfPageLastIndex

doc.Save("annex.pdf")
doc.Close()
```

A fixed rectangle shows the first rendered page and nothing more:

```csharp
PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();

PdfWordElement card = new PdfWordElement(50, 100, 300, 200, "summary.docx");
page.Add(card);

doc.Save("card.pdf");
doc.Close();
```

```vbnet
Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()

Dim card As New PdfWordElement(50, 100, 300, 200, "summary.docx")
page.Add(card)

doc.Save("card.pdf")
doc.Close()
```

### On a template

Added to a header, footer or stamp template (`PdfDocument.Templates`), the element draws the first rendered page on every page the template applies to and re-creates its web links on each of them; the outline is not carried, since a band has no single place in a reading order.

### Tagged documents

On a page of a tagged document the region carries its own structure: the Word document's headings, paragraphs, lists, tables and links join the host document's logical structure, in the reading order, at the place the region was drawn - so a document declaring PDF/UA-1 or PDF/UA-2 stays conformant with a Word region on it. `PdfWordElement.Tagged` asks for that structure on a document that is not tagged itself. A single `PdfPageElement.TagType` over the whole region is refused, the region being a structure rather than one element; set `PdfPageElement.Artifact` to declare the whole region decorative instead.

In a header, footer or stamp the element is still refused on a tagged document unless it is decorative: a band repeats on every page, so its content has no single place in a reading order.

## Word sections

`PdfWordSection` puts a Word document into the header or footer band of a converted document, the way `PdfHtmlSection` puts html there. The document is given as a file path or a stream, in any format the Word converter reads. It is rendered once, with every page set to the size of the band and no margins of its own - the band is the paper - and the first rendered page is drawn on every page that shows the band, scaled to the band's width and cut at its height, never stretched. Its web links stay clickable on every page.

A section is independent of the converter it heads: a Word band can head the pages of a `WordToPdf` conversion or of an `HtmlToPdf` one, and an html band can head a Word conversion.

> **Note:** `PdfWordSection` is available only in the full commercial SelectPdf library.

### Sample Code

A Word file as the header of a Word conversion, and a page-number footer. `PdfWordSection.PageRanges` chooses which rendered page the band shows when the header document has several.

```csharp
WordToPdf converter = new WordToPdf();

converter.Options.DisplayHeader = true;
converter.Header.Height = 50;
converter.Header.Add(new PdfWordSection("header.docx"));

converter.Options.DisplayFooter = true;
converter.Footer.Height = 30;
converter.Footer.Add(new PdfTextSection(0, 10, 500,
    "Page {page_number} of {total_pages}",
    PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 9),
    new PdfColor(80, 80, 80)));

PdfDocument doc = converter.ConvertFile("report.docx");
doc.Save("report.pdf");
doc.Close();
```

```vbnet
Dim converter As New WordToPdf()

converter.Options.DisplayHeader = True
converter.Header.Height = 50
converter.Header.Add(New PdfWordSection("header.docx"))

converter.Options.DisplayFooter = True
converter.Footer.Height = 30
converter.Footer.Add(New PdfTextSection(0, 10, 500,
    "Page {page_number} of {total_pages}",
    PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 9),
    New PdfColor(80, 80, 80)))

Dim doc As PdfDocument = converter.ConvertFile("report.docx")
doc.Save("report.pdf")
doc.Close()
```

The same band heading an html conversion:

```csharp
HtmlToPdf htmlConverter = new HtmlToPdf();
htmlConverter.Options.DisplayHeader = true;
htmlConverter.Header.Height = 50;
htmlConverter.Header.Add(new PdfWordSection("header.docx"));

PdfDocument page = htmlConverter.ConvertUrl("https://selectpdf.com");
page.Save("page.pdf");
page.Close();
```

```vbnet
Dim htmlConverter As New HtmlToPdf()
htmlConverter.Options.DisplayHeader = True
htmlConverter.Header.Height = 50
htmlConverter.Header.Add(New PdfWordSection("header.docx"))

Dim page As PdfDocument = htmlConverter.ConvertUrl("https://selectpdf.com")
page.Save("page.pdf")
page.Close()
```

### The band is the paper

The document's own page margins are not kept: a file made in Word carries one-inch margins, and a 50-point band cannot hold two of them - the text would land entirely outside the band. Place the content inside the band with `PdfSectionElement.X`, `PdfSectionElement.Y`, `PdfSectionElement.Width` and `PdfSectionElement.Height` instead.
