# Excel and PowerPoint to PDF

Converted from the shipped SelectPdf.Universal v26.6 help topics. C# and VB.NET samples are the official ones.

## The Excel to PDF converter

Using the SelectPdf Excel to Pdf Converter for .NET, an Excel workbook can be converted to pdf with a few lines of code. The converter reads the formats Microsoft Excel writes - xlsx, xlsm, xltx, xltm, xls, xlt - plus comma and tab separated values, the Excel 2003 XML spreadsheet (SpreadsheetML) and markdown, whose tables become worksheets. The conversion runs in-process: no engine is launched.

> **Note:** The Excel to Pdf converter is available only in the full commercial SelectPdf library. It is not available in the free SelectPdf community edition.

The `ExcelToPdf` class exposes `ExcelToPdf.ConvertFile(string)`, `ExcelToPdf.ConvertStream(Stream)` and `ExcelToPdf.ConvertBytes(byte[])`, each with an overload that names the `ExcelFormat` to read. Without one, the format is taken from the file extension, or detected from the content for a stream or a byte array (xlsx family, xls, SpreadsheetML, csv). Tab separated values and markdown have no signature and must be named when given as a stream.

Every visible sheet is converted, in workbook order, as Excel prints it: its print area, its page breaks and its own printed header and footer. Hidden sheets are left out. `ExcelToPdfOptions.Worksheets` converts only the sheets it names. Each sheet becomes an entry in the outline of the pdf (`ExcelToPdfOptions.SheetBookmarks`).

The result is a `PdfDocument`, exactly as the html converter returns one: it can be modified further, saved to a file, a stream or a byte array, and must be closed. The `ExcelToPdf.Options`, `ExcelToPdf.Header` and `ExcelToPdf.Footer` properties mirror the html and Word converters', so a document setup written for `HtmlToPdf` applies here unchanged - page size and margins, header and footer bands, PDF/A and PDF/X standards, security, viewer preferences, document properties, compression, page ranges and single-page mode. The options are reviewed in "Excel to PDF options" (office-to-pdf.md).

### Page setup: the sheets' own, or the converter's

Every sheet of a workbook has a page setup of its own, so `ExcelToPdfOptions.PageSetup` decides what the page size options mean:

- `ExcelPageSetup.Document` (the default) prints every sheet on the paper size, orientation and margins its own page setup gives it, as Excel prints it. The page size and orientation options are ignored; the margins and the header and footer bands are added around the printed page. With zero margins and no bands every output page is the page Excel prints.
- `ExcelPageSetup.Options` lays every sheet out on the page size, orientation and margins of the options, replacing the sheets' own paper and margins.

`ExcelToPdfOptions.Layout` decides how the content of a sheet is scaled onto its pages: the sheet's own print scaling (the default), its actual size, the whole sheet on one page, all its columns on the width of one page, or all its rows on the height of one page (`ExcelLayout`). `ExcelToPdfOptions.GridLines` draws or hides the cell gridlines; by default each sheet's print setting decides.

### Sample Code

This sample converts two sheets of a workbook, each with all its columns fitted to the width of an A4 landscape page, under a header band and page numbers, and saves it as PDF/A-2b.

```csharp
ExcelToPdf converter = new ExcelToPdf();

// lay the sheets out on the page set here, all the columns on one page
converter.Options.PageSetup = ExcelPageSetup.Options;
converter.Options.PdfPageSize = PdfPageSize.A4;
converter.Options.PdfPageOrientation = PdfPageOrientation.Landscape;
converter.Options.MarginLeft = 20;
converter.Options.MarginRight = 20;
converter.Options.Layout = ExcelLayout.FitAllColumnsOnOnePage;
converter.Options.Worksheets = "Summary, Sales";
converter.Options.PdfStandard = PdfStandard.PdfA2B;

// a text header band and a page-number footer
converter.Options.DisplayHeader = true;
converter.Header.Height = 40;
converter.Header.Add(new PdfTextSection(0, 12, 500, "Quarterly figures",
    PdfFont.CreateStandardFont(PdfStandardFont.HelveticaBold, 11),
    new PdfColor(44, 62, 80)));
converter.Options.DisplayFooter = true;
converter.Footer.Height = 30;
converter.Footer.Add(new PdfTextSection(0, 10, 500,
    "Page {page_number} of {total_pages}",
    PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 9),
    new PdfColor(80, 80, 80)));

// the format is taken from the extension
PdfDocument doc = converter.ConvertFile("sales.xlsx");

// one outline entry per sheet
int sheets = doc.Bookmarks.Count;

doc.Save("sales.pdf");
doc.Close();
```

```vbnet
Dim converter As New ExcelToPdf()

' lay the sheets out on the page set here, all the columns on one page
converter.Options.PageSetup = ExcelPageSetup.Options
converter.Options.PdfPageSize = PdfPageSize.A4
converter.Options.PdfPageOrientation = PdfPageOrientation.Landscape
converter.Options.MarginLeft = 20
converter.Options.MarginRight = 20
converter.Options.Layout = ExcelLayout.FitAllColumnsOnOnePage
converter.Options.Worksheets = "Summary, Sales"
converter.Options.PdfStandard = PdfStandard.PdfA2B

' a text header band and a page-number footer
converter.Options.DisplayHeader = True
converter.Header.Height = 40
converter.Header.Add(New PdfTextSection(0, 12, 500, "Quarterly figures",
    PdfFont.CreateStandardFont(PdfStandardFont.HelveticaBold, 11),
    New PdfColor(44, 62, 80)))
converter.Options.DisplayFooter = True
converter.Footer.Height = 30
converter.Footer.Add(New PdfTextSection(0, 10, 500,
    "Page {page_number} of {total_pages}",
    PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 9),
    New PdfColor(80, 80, 80)))

' the format is taken from the extension
Dim doc As PdfDocument = converter.ConvertFile("sales.xlsx")

' one outline entry per sheet
Dim sheets As Integer = doc.Bookmarks.Count

doc.Save("sales.pdf")
doc.Close()
```

A csv file given as a stream must name its format:

```csharp
ExcelToPdf converter = new ExcelToPdf();
converter.Options.GridLines = ExcelGridLines.Visible;

using (FileStream csv = File.OpenRead("orders.csv"))
{
    PdfDocument doc = converter.ConvertStream(csv, ExcelFormat.Csv);
    doc.Save("orders.pdf");
    doc.Close();
}
```

```vbnet
Dim converter As New ExcelToPdf()
converter.Options.GridLines = ExcelGridLines.Visible

Using csv As FileStream = File.OpenRead("orders.csv")
    Dim doc As PdfDocument = converter.ConvertStream(csv, ExcelFormat.Csv)
    doc.Save("orders.pdf")
    doc.Close()
End Using
```

### Fonts

Every font the workbook uses is embedded in the pdf as a subset. A font family the host does not have is served from the fonts the library bundles - the Liberation faces, metric-compatible with Arial, Times New Roman and Courier New - so the same workbook converts on a Windows server, a Linux container with no fonts installed, or a Mac. Scripts those faces do not cover (CJK, Arabic, Indic) need fonts installed on the host; `ExcelToPdfOptions.AutoDetectComplexScript` shapes complex scripts the workbook does not mark.

### Archiving standards and accessibility

Every PDF/A level and PDF/X-1a are produced the way the html converter produces them, through `ExcelToPdfOptions.PdfStandard`.

Accessible (tagged) output is declared the way it is on the html and Word converters: `ExcelToPdfOptions.Tagged` builds the logical structure - the sheets' tables with their header cells, charts and pictures as figures with their alternative text, and links - and `ExcelToPdfOptions.AccessibilityStandard` declares PDF/UA-1 or PDF/UA-2 (which is written as PDF 2.0). A declared document needs a `ExcelToPdfOptions.Title` and a `ExcelToPdfOptions.Language`. `PdfStandard.PdfA3A` turns tagging on by itself, and combines with PDF/UA-1; PDF/A-4 is the archiving level that combines with PDF/UA-2.

A chart is described by its own alternative text, else by its title; give every picture and chart a description in Excel for a document that is accessible in substance and not only in form. Check the produced document at the level it declares with `PdfValidator.Validate` ("Validating PDF/A, PDF/UA and PDF/X" (accessibility-and-compliance.md)).

## Excel to PDF options

`ExcelToPdfOptions`, reached through `ExcelToPdf.Options`, controls the conversion and the generated document. Most of it is the same surface as `HtmlToPdfOptions`, with the same defaults, so it is only listed here; the properties that belong to the Excel engine are described.

### The options shared with the html converter

| Property | What it does |
|---|---|
| `ExcelToPdfOptions.PdfPageSize`, `ExcelToPdfOptions.PdfPageCustomSize`, `ExcelToPdfOptions.PdfPageOrientation` | The page of the generated document. Applied only under the `Options` page setup; under `Document` each sheet keeps the paper of its own page setup. |
| `ExcelToPdfOptions.MarginLeft`, `MarginRight`, `MarginTop`, `MarginBottom` | Margins in points, zero by default. Applied under both page setups: they surround the printed page (Document) or replace the sheets' margins (Options). |
| `ExcelToPdfOptions.DisplayHeader`, `ExcelToPdfOptions.DisplayFooter` | Show the bands built through `ExcelToPdf.Header` and `ExcelToPdf.Footer`. Text, image, html, Word, Excel and PowerPoint sections all work; see "Excel sections" (office-to-pdf.md). |
| `ExcelToPdfOptions.ExternalLinksEnabled`, `ExcelToPdfOptions.InternalLinksEnabled` | Carry the workbook's web links and its links to other cells and sheets as clickable pdf links. Both on by default. |
| `ExcelToPdfOptions.PageRanges` | Keep only the named rendered pages, e.g. `"1,3-5"`. A range that selects none of the rendered pages is refused. |
| `ExcelToPdfOptions.GenerateSinglePagePdf` | Stack every rendered page onto one tall pdf page. |
| `ExcelToPdfOptions.PdfStandard`, `ExcelToPdfOptions.ColorSpace` | PDF/A-1b to PDF/A-4 and PDF/X-1a, and the color space, produced as the html converter produces them. |
| `ExcelToPdfOptions.Tagged`, `ExcelToPdfOptions.AccessibilityStandard`, `ExcelToPdfOptions.Title`, `ExcelToPdfOptions.Language` | Tagged (accessible) output: the sheets' tables, charts, pictures and links become the logical structure of the pdf, declared as PDF/UA-1 or PDF/UA-2 with the title and the language a declared document must carry. See the Standards section of the converter topic. |
| `ExcelToPdfOptions.SecurityOptions`, `ExcelToPdfOptions.ViewerPreferences`, `ExcelToPdfOptions.PdfDocumentInformation` | Passwords and permissions, how a viewer opens the document, and its title, author, subject and keywords. |
| `ExcelToPdfOptions.JpegCompressionEnabled`, `ExcelToPdfOptions.JpegCompressionLevel`, `ExcelToPdfOptions.PdfCompressionLevel` | Image and stream compression, with the html converter's defaults. |
| `ExcelToPdfOptions.ImagesMaxResolution` | The highest resolution, in pixels per inch, an image is kept at; images drawn at a higher resolution are resampled down to it. 0 (the default) keeps every image as it is. |
| `ExcelToPdfOptions.LFN` | A diagnostic log file written during the conversion. |

### The Excel engine's own options

| Property | Default | What it does |
|---|---|---|
| `ExcelToPdfOptions.PageSetup` | `Document` | Whether every sheet is printed on the paper and margins of its own page setup (`Document`) or laid out on the page size, orientation and margins of the options (`Options`). See "The Excel to PDF converter" (office-to-pdf.md). |
| `ExcelToPdfOptions.Password` | none | The password of an encrypted workbook. |
| `ExcelToPdfOptions.Worksheets` | empty | The sheets converted, by name, comma separated, e.g. `"Summary, Q1"`. Empty converts every visible sheet in workbook order; naming a sheet the workbook does not have fails the conversion. |
| `ExcelToPdfOptions.Layout` | `Automatic` | How the content of each sheet is scaled onto its pages: the sheet's own print scaling, its actual size, the whole sheet on one page, all its columns on one page or all its rows on one page (`ExcelLayout`). |
| `ExcelToPdfOptions.GridLines` | `Automatic` | Whether the cell gridlines are drawn: as each sheet's print setting says, always, or never (`ExcelGridLines`). |
| `ExcelToPdfOptions.SheetBookmarks` | `true` | One entry in the pdf outline per sheet, named after it. |
| `ExcelToPdfOptions.SheetHeadersAndFooters` | `true` | Draw the headers and footers set in the sheets' own page setup. They are independent of the converter's header and footer bands. |
| `ExcelToPdfOptions.ConvertBlankSheets`, `ExcelToPdfOptions.ConvertBlankPages` | `false` | Keep a page for a sheet with no content, and keep the empty pages inside a sheet's printed range. |
| `ExcelToPdfOptions.PreserveFormFields` | `false` | Turn the sheets' form controls (check boxes, option buttons, combo boxes) into fillable pdf form fields instead of drawings. |
| `ExcelToPdfOptions.AutoDetectComplexScript` | `false` | Detect and shape complex scripts (Arabic, Hebrew, Thai and the like) even where the workbook does not mark them. |

### The conversion result

After a conversion, `ExcelToPdf.ConversionResult` describes the document produced: `ExcelToPdfResult.PdfPageCount`, `ExcelToPdfResult.PdfPageSize`, `ExcelToPdfResult.PdfPageLastIndex`, `ExcelToPdfResult.PdfPageLastRectangle` and `ExcelToPdfResult.PdfPagesRectangles`, one rectangle per rendered page in the coordinates of the pdf page it landed on. A workbook that cannot be read or rendered fails the conversion with `ExcelToPdfException`.

## Excel workbooks as PDF elements

`PdfExcelElement` draws an Excel workbook into a region of a pdf page, the way `PdfWordElement` draws a Word document there. The workbook is given as a file path or a stream, in any format the Excel converter reads, and is rendered in-process at render time with every sheet laid out on pages the size of the region and no margins of its own - the region is the paper. Its links stay clickable, and each sheet becomes an entry in the outline of the host document (`PdfExcelElement.SheetBookmarks`).

Coordinates and sizes are in the page's client-area coordinate system, relative to the top-left corner of the writable area inside the page margins. `PdfExcelElement.Width` defaults to the client width minus X; `PdfExcelElement.Height` decides how the content flows:

- A fixed height that fits its page draws the first rendered page, cut at the box.
- The default, -1, lets the content flow onto the pages that follow, using the document's next page when there is one and appending one when there is not.
- A fixed height taller than what is left of the page continues on the pages that follow, the same way, until that much height has been drawn.

`PdfExcelElement.Layout`, `PdfExcelElement.GridLines`, `PdfExcelElement.Worksheets`, `PdfExcelElement.Password`, `PdfExcelElement.Format` and `PdfExcelElement.PageRanges` work as on the converter.

> **Note:** `PdfExcelElement` is available only in the full commercial SelectPdf library.

### Sample Code

A title drawn from code, then a workbook flowing from below it onto the pages that follow.

```csharp
PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();

PdfFont titleFont =
    PdfFont.CreateStandardFont(PdfStandardFont.HelveticaBold, 20);
page.Add(new PdfTextElement(0, 0, "Annex B - the sales figures", titleFont,
    new PdfColor(44, 62, 80)));

// from 60pt down the page, all the columns on the width of the page
PdfExcelElement sales = new PdfExcelElement(0, 60, "sales.xlsx");
sales.Layout = ExcelLayout.FitAllColumnsOnOnePage;
PdfRenderingResult result = page.Add(sales);

// where each rendered page landed, and the page the region ended on
SelectPdf.Universal.Drawing.RectangleF[] slices = sales.PdfPagesRectangles;
int lastPage = result.PdfPageLastIndex;

doc.Save("annex.pdf");
doc.Close();
```

```vbnet
Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()

Dim titleFont As PdfFont = _
    PdfFont.CreateStandardFont(PdfStandardFont.HelveticaBold, 20)
page.Add(New PdfTextElement(0, 0, "Annex B - the sales figures", titleFont, _
    New PdfColor(44, 62, 80)))

' from 60pt down the page, all the columns on the width of the page
Dim sales As New PdfExcelElement(0, 60, "sales.xlsx")
sales.Layout = ExcelLayout.FitAllColumnsOnOnePage
Dim result As PdfRenderingResult = page.Add(sales)

' where each rendered page landed, and the page the region ended on
Dim slices As SelectPdf.Universal.Drawing.RectangleF() = sales.PdfPagesRectangles
Dim lastPage As Integer = result.PdfPageLastIndex

doc.Save("annex.pdf")
doc.Close()
```

A fixed rectangle shows one sheet, fitted on the first rendered page:

```csharp
PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();

PdfExcelElement card = new PdfExcelElement(50, 100, 400, 250, "sales.xlsx");
card.Worksheets = "Summary";
card.Layout = ExcelLayout.FitSheetOnOnePage;
page.Add(card);

doc.Save("card.pdf");
doc.Close();
```

```vbnet
Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()

Dim card As New PdfExcelElement(50, 100, 400, 250, "sales.xlsx")
card.Worksheets = "Summary"
card.Layout = ExcelLayout.FitSheetOnOnePage
page.Add(card)

doc.Save("card.pdf")
doc.Close()
```

### On a template

Added to a header, footer or stamp template (`PdfDocument.Templates`), the element draws the first rendered page on every page the template applies to and re-creates its web links on each of them; the outline is not carried, since a band has no single place in a reading order.

### Tagged documents

On a page of a tagged document the region carries its own structure: the workbook's tables, charts, pictures and links join the host document's logical structure, in the reading order, at the place the region was drawn - so a document declaring PDF/UA-1 or PDF/UA-2 stays conformant with an Excel region on it. `PdfExcelElement.Tagged` asks for that structure on a document that is not tagged itself. Set `PdfPageElement.Artifact` to declare the whole region decorative instead.

In a header, footer or stamp the element is refused on a tagged document unless it is decorative: a band repeats on every page, so its content has no single place in a reading order.

## Excel sections

`PdfExcelSection` puts an Excel workbook into the header or footer band of a converted document, the way `PdfHtmlSection` puts html there. The workbook is given as a file path or a stream, in any format the Excel converter reads. It is rendered once, every sheet laid out on a page the size of the band with no margins of its own - the band is the paper - and the first rendered page is drawn on every page that shows the band, scaled to the band's width and cut at its height, never stretched. Its web links stay clickable on every page.

A band shows one page, so the sheet is fitted onto it by default: `PdfExcelSection.Layout` is `ExcelLayout.FitSheetOnOnePage`. `PdfExcelSection.Worksheets` picks the sheet and `PdfExcelSection.PageRanges` the rendered page the band shows.

A section is independent of the converter it heads: an Excel band can head the pages of an `ExcelToPdf` conversion or of an `HtmlToPdf`, Word or PowerPoint one, and any other band can head an Excel conversion.

> **Note:** `PdfExcelSection` is available only in the full commercial SelectPdf library.

### Sample Code

A one-sheet summary workbook as the header of an Excel conversion, and a page-number footer:

```csharp
ExcelToPdf converter = new ExcelToPdf();

converter.Options.DisplayHeader = true;
converter.Header.Height = 60;
PdfExcelSection totals = new PdfExcelSection("totals.xlsx");
totals.Worksheets = "Totals";
converter.Header.Add(totals);

converter.Options.DisplayFooter = true;
converter.Footer.Height = 30;
converter.Footer.Add(new PdfTextSection(0, 10, 500,
    "Page {page_number} of {total_pages}",
    PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 9),
    new PdfColor(80, 80, 80)));

PdfDocument doc = converter.ConvertFile("sales.xlsx");
doc.Save("sales.pdf");
doc.Close();
```

```vbnet
Dim converter As New ExcelToPdf()

converter.Options.DisplayHeader = True
converter.Header.Height = 60
Dim totals As New PdfExcelSection("totals.xlsx")
totals.Worksheets = "Totals"
converter.Header.Add(totals)

converter.Options.DisplayFooter = True
converter.Footer.Height = 30
converter.Footer.Add(New PdfTextSection(0, 10, 500,
    "Page {page_number} of {total_pages}",
    PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 9),
    New PdfColor(80, 80, 80)))

Dim doc As PdfDocument = converter.ConvertFile("sales.xlsx")
doc.Save("sales.pdf")
doc.Close()
```

The same band heading an html conversion, placed at the right of the band:

```csharp
HtmlToPdf htmlConverter = new HtmlToPdf();
htmlConverter.Options.DisplayHeader = true;
htmlConverter.Header.Height = 60;
htmlConverter.Header.Add(new PdfExcelSection(300, 0, 230, "totals.xlsx"));

PdfDocument page = htmlConverter.ConvertUrl("https://selectpdf.com");
page.Save("page.pdf");
page.Close();
```

```vbnet
Dim htmlConverter As New HtmlToPdf()
htmlConverter.Options.DisplayHeader = True
htmlConverter.Header.Height = 60
htmlConverter.Header.Add(New PdfExcelSection(300, 0, 230, "totals.xlsx"))

Dim page As PdfDocument = htmlConverter.ConvertUrl("https://selectpdf.com")
page.Save("page.pdf")
page.Close()
```

### The band is the paper

The sheets' own page margins are not kept: a band a few dozen points high cannot hold them, and the content would land outside it. Place the content inside the band with `PdfSectionElement.X`, `PdfSectionElement.Y`, `PdfSectionElement.Width` and `PdfSectionElement.Height` instead.

## The PowerPoint to PDF converter

Using the SelectPdf PowerPoint to Pdf Converter for .NET, a PowerPoint presentation can be converted to pdf with a few lines of code - its slides, its notes pages or handouts with several slides on a page. The converter reads pptx, pptm, potx, potm, ppsx and ppsm files. The conversion runs in-process: no engine is launched.

> **Note:** The PowerPoint to Pdf converter is available only in the full commercial SelectPdf library. It is not available in the free SelectPdf community edition.

The `PowerPointToPdf` class exposes `PowerPointToPdf.ConvertFile(string)`, `PowerPointToPdf.ConvertStream(Stream)` and `PowerPointToPdf.ConvertBytes(byte[])`.

The result is a `PdfDocument`, exactly as the html converter returns one: it can be modified further, saved to a file, a stream or a byte array, and must be closed. The `PowerPointToPdf.Options`, `PowerPointToPdf.Header` and `PowerPointToPdf.Footer` properties mirror the html and Word converters', so a document setup written for `HtmlToPdf` applies here unchanged - page size and margins, header and footer bands, PDF/A and PDF/X standards, security, viewer preferences, document properties, compression, page ranges and single-page mode. The options are reviewed in "PowerPoint to PDF options" (office-to-pdf.md).

### Slides, notes pages or handouts

`PowerPointToPdfOptions.Publish` decides what the pdf holds:

- `PowerPointPublish.Slides` (the default): one page per slide.
- `PowerPointPublish.NotesPages`: one page per slide, the slide with its speaker notes below it.
- `PowerPointPublish.Handouts`: pages holding `PowerPointToPdfOptions.SlidesPerHandout` slides each (1, 2, 3, 4, 6 or 9; 6 by default). Notes pages and handouts are not produced as a tagged (accessible) document: publish the slides for one.

The slides hidden in the presentation are left out unless `PowerPointToPdfOptions.ShowHiddenSlides` is set.

### Page setup: the slide size, or the converter's

`PowerPointToPdfOptions.PageSetup` decides what the page size options mean:

- `PowerPointPageSetup.Document` (the default) makes every page a slide (or a notes or handout page) at the size the presentation defines. The page size and orientation options are ignored; the margins and the header and footer bands are added around it.
- `PowerPointPageSetup.Options` scales every slide uniformly to fit the page size and orientation of the options, inside their margins and the bands - for example, a widescreen deck printed on A4 landscape pages.

### Sample Code

This sample converts a presentation to handouts of four slides on A4 portrait pages, with page numbers.

```csharp
PowerPointToPdf converter = new PowerPointToPdf();

// four slides on each page, the pages A4 portrait
converter.Options.Publish = PowerPointPublish.Handouts;
converter.Options.SlidesPerHandout = 4;
converter.Options.PageSetup = PowerPointPageSetup.Options;
converter.Options.PdfPageSize = PdfPageSize.A4;
converter.Options.PdfPageOrientation = PdfPageOrientation.Portrait;

// a page-number footer
converter.Options.DisplayFooter = true;
converter.Footer.Height = 30;
converter.Footer.Add(new PdfTextSection(0, 10, 500,
    "Page {page_number} of {total_pages}",
    PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 9),
    new PdfColor(80, 80, 80)));

PdfDocument doc = converter.ConvertFile("deck.pptx");
doc.Save("handouts.pdf");
doc.Close();
```

```vbnet
Dim converter As New PowerPointToPdf()

' four slides on each page, the pages A4 portrait
converter.Options.Publish = PowerPointPublish.Handouts
converter.Options.SlidesPerHandout = 4
converter.Options.PageSetup = PowerPointPageSetup.Options
converter.Options.PdfPageSize = PdfPageSize.A4
converter.Options.PdfPageOrientation = PdfPageOrientation.Portrait

' a page-number footer
converter.Options.DisplayFooter = True
converter.Footer.Height = 30
converter.Footer.Add(New PdfTextSection(0, 10, 500,
    "Page {page_number} of {total_pages}",
    PdfFont.CreateStandardFont(PdfStandardFont.Helvetica, 9),
    New PdfColor(80, 80, 80)))

Dim doc As PdfDocument = converter.ConvertFile("deck.pptx")
doc.Save("handouts.pdf")
doc.Close()
```

The slides themselves, as an accessible PDF/UA-1 document:

```csharp
PowerPointToPdf converter = new PowerPointToPdf();
converter.Options.AccessibilityStandard = PdfUAStandard.UA1;
converter.Options.Title = "Quarterly review";
converter.Options.Language = "en-US";

byte[] pptx = File.ReadAllBytes("deck.pptx");
PdfDocument doc = converter.ConvertBytes(pptx);
doc.Save("deck.pdf");
doc.Close();
```

```vbnet
Dim converter As New PowerPointToPdf()
converter.Options.AccessibilityStandard = PdfUAStandard.UA1
converter.Options.Title = "Quarterly review"
converter.Options.Language = "en-US"

Dim pptx As Byte() = File.ReadAllBytes("deck.pptx")
Dim doc As PdfDocument = converter.ConvertBytes(pptx)
doc.Save("deck.pdf")
doc.Close()
```

### Fonts

Every font the presentation uses is embedded in the pdf as a subset (or complete, with `PowerPointToPdfOptions.EmbedCompleteFonts`). A font family the host does not have is served from the fonts the library bundles - the Liberation faces, metric-compatible with Arial, Times New Roman and Courier New - so the same presentation converts on a Windows server, a Linux container with no fonts installed, or a Mac. Scripts those faces do not cover (CJK, Arabic, Indic) need fonts installed on the host.

### Archiving standards and accessibility

Every PDF/A level and PDF/X-1a are produced the way the html converter produces them, through `PowerPointToPdfOptions.PdfStandard`.

Accessible (tagged) output is declared the way it is on the html and Word converters: `PowerPointToPdfOptions.Tagged` builds the logical structure - the slides' titles, text, lists, tables, pictures with their descriptions and links - and `PowerPointToPdfOptions.AccessibilityStandard` declares PDF/UA-1 or PDF/UA-2 (which is written as PDF 2.0). A declared document needs a `PowerPointToPdfOptions.Title` and a `PowerPointToPdfOptions.Language`. `PdfStandard.PdfA3A` turns tagging on by itself, and combines with PDF/UA-1; PDF/A-4 is the archiving level that combines with PDF/UA-2.

Give every picture and chart a description in PowerPoint for a document that is accessible in substance and not only in form. Check the produced document at the level it declares with `PdfValidator.Validate` ("Validating PDF/A, PDF/UA and PDF/X" (accessibility-and-compliance.md)).

## PowerPoint to PDF options

`PowerPointToPdfOptions`, reached through `PowerPointToPdf.Options`, controls the conversion and the generated document. Most of it is the same surface as `HtmlToPdfOptions`, with the same defaults, so it is only listed here; the properties that belong to the PowerPoint engine are described.

### The options shared with the html converter

| Property | What it does |
|---|---|
| `PowerPointToPdfOptions.PdfPageSize`, `PowerPointToPdfOptions.PdfPageCustomSize`, `PowerPointToPdfOptions.PdfPageOrientation` | The page of the generated document. Applied only under the `Options` page setup; under `Document` every page has the size of the presentation's slides. |
| `PowerPointToPdfOptions.MarginLeft`, `MarginRight`, `MarginTop`, `MarginBottom` | Margins in points, zero by default, around the slide under both page setups. |
| `PowerPointToPdfOptions.DisplayHeader`, `PowerPointToPdfOptions.DisplayFooter` | Show the bands built through `PowerPointToPdf.Header` and `PowerPointToPdf.Footer`. Text, image, html, Word, Excel and PowerPoint sections all work; see "PowerPoint sections" (office-to-pdf.md). |
| `PowerPointToPdfOptions.ExternalLinksEnabled`, `PowerPointToPdfOptions.InternalLinksEnabled` | Carry the presentation's web links and its links to other slides as clickable pdf links. Both on by default. |
| `PowerPointToPdfOptions.PageRanges` | Keep only the named rendered pages, e.g. `"1,3-5"`. A range that selects none of the rendered pages is refused. |
| `PowerPointToPdfOptions.GenerateSinglePagePdf` | Stack every rendered page onto one tall pdf page. |
| `PowerPointToPdfOptions.PdfStandard`, `PowerPointToPdfOptions.ColorSpace` | PDF/A-1b to PDF/A-4 and PDF/X-1a, and the color space, produced as the html converter produces them. |
| `PowerPointToPdfOptions.Tagged`, `PowerPointToPdfOptions.AccessibilityStandard`, `PowerPointToPdfOptions.Title`, `PowerPointToPdfOptions.Language` | Tagged (accessible) output: the slides' structure becomes the logical structure of the pdf, declared as PDF/UA-1 or PDF/UA-2 with the title and the language a declared document must carry. See the Standards section of the converter topic. |
| `PowerPointToPdfOptions.SecurityOptions`, `PowerPointToPdfOptions.ViewerPreferences`, `PowerPointToPdfOptions.PdfDocumentInformation` | Passwords and permissions, how a viewer opens the document, and its title, author, subject and keywords. |
| `PowerPointToPdfOptions.JpegCompressionEnabled`, `PowerPointToPdfOptions.JpegCompressionLevel`, `PowerPointToPdfOptions.PdfCompressionLevel` | Image and stream compression, with the html converter's defaults. |
| `PowerPointToPdfOptions.ImagesMaxResolution` | The highest resolution, in pixels per inch, an image is kept at; images drawn at a higher resolution are resampled down to it. 0 (the default) keeps every image as it is. |
| `PowerPointToPdfOptions.LFN` | A diagnostic log file written during the conversion. |

### The PowerPoint engine's own options

| Property | Default | What it does |
|---|---|---|
| `PowerPointToPdfOptions.PageSetup` | `Document` | Whether every page has the size of the presentation's slides (`Document`) or the slides are scaled onto the page size, orientation and margins of the options (`Options`). See "The PowerPoint to PDF converter" (office-to-pdf.md). |
| `PowerPointToPdfOptions.Password` | none | The password of an encrypted presentation. |
| `PowerPointToPdfOptions.Publish` | `Slides` | What is published: one page per slide, one notes page per slide, or handout pages (`PowerPointPublish`). |
| `PowerPointToPdfOptions.SlidesPerHandout` | `6` | How many slides a handout page holds: 1, 2, 3, 4, 6 or 9. Used with `Handouts`. |
| `PowerPointToPdfOptions.ShowHiddenSlides` | `false` | Convert the slides hidden in the presentation too. |
| `PowerPointToPdfOptions.PreserveFormFields` | `false` | Turn the presentation's form fields into fillable pdf form fields. |
| `PowerPointToPdfOptions.EmbedCompleteFonts` | `false` | Embed the complete font programs rather than the subsets the presentation uses. |
| `PowerPointToPdfOptions.OptimizeIdenticalImages` | `true` | Store an image used several times once. |

### The conversion result

After a conversion, `PowerPointToPdf.ConversionResult` describes the document produced: `PowerPointToPdfResult.PdfPageCount`, `PowerPointToPdfResult.PdfPageSize`, `PowerPointToPdfResult.PdfPageLastIndex`, `PowerPointToPdfResult.PdfPageLastRectangle` and `PowerPointToPdfResult.PdfPagesRectangles`, one rectangle per rendered page in the coordinates of the pdf page it landed on. A presentation that cannot be read or rendered fails the conversion with `PowerPointToPdfException`.

## PowerPoint presentations as PDF elements

`PdfPowerPointElement` draws the slides of a PowerPoint presentation into a region of a pdf page, the way `PdfWordElement` draws a Word document there. The presentation is given as a file path or a stream (pptx, pptm, potx, potm, ppsx or ppsm) and is rendered in-process at render time. Each rendered page - a slide, a notes page or a handout page, per `PdfPowerPointElement.Publish` - keeps its shape, scaled to the element's width: the pages are stacked from the element's position and continue on the pages that follow. The links stay clickable.

Coordinates and sizes are in the page's client-area coordinate system, relative to the top-left corner of the writable area inside the page margins. `PdfPowerPointElement.Width` defaults to the client width minus X; `PdfPowerPointElement.Height` decides how the content flows:

- A fixed height that fits its page shows what fits in it.
- The default, -1, lets the slides flow onto the pages that follow, using the document's next page when there is one and appending one when there is not.
- A fixed height taller than what is left of the page continues on the pages that follow, the same way, until that much height has been drawn.

> **Note:** `PdfPowerPointElement` is available only in the full commercial SelectPdf library.

### Sample Code

The slides of a presentation, 300 points wide, below a title drawn from code:

```csharp
PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();

PdfFont titleFont =
    PdfFont.CreateStandardFont(PdfStandardFont.HelveticaBold, 20);
page.Add(new PdfTextElement(0, 0, "Annex C - the presentation", titleFont,
    new PdfColor(44, 62, 80)));

// the slides stacked from 50pt down, continuing on the pages after it
PdfPowerPointElement deck = new PdfPowerPointElement(0, 50, 300, "deck.pptx");
PdfRenderingResult result = page.Add(deck);

// where each slide landed, and the page the region ended on
SelectPdf.Universal.Drawing.RectangleF[] slides = deck.PdfPagesRectangles;
int lastPage = result.PdfPageLastIndex;

doc.Save("annex.pdf");
doc.Close();
```

```vbnet
Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()

Dim titleFont As PdfFont = _
    PdfFont.CreateStandardFont(PdfStandardFont.HelveticaBold, 20)
page.Add(New PdfTextElement(0, 0, "Annex C - the presentation", titleFont, _
    New PdfColor(44, 62, 80)))

' the slides stacked from 50pt down, continuing on the pages after it
Dim deck As New PdfPowerPointElement(0, 50, 300, "deck.pptx")
Dim result As PdfRenderingResult = page.Add(deck)

' where each slide landed, and the page the region ended on
Dim slides As SelectPdf.Universal.Drawing.RectangleF() = deck.PdfPagesRectangles
Dim lastPage As Integer = result.PdfPageLastIndex

doc.Save("annex.pdf")
doc.Close()
```

One slide, the third, in a fixed rectangle:

```csharp
PdfDocument doc = new PdfDocument();
PdfPage page = doc.AddPage();

PdfPowerPointElement slide =
    new PdfPowerPointElement(50, 100, 400, 225, "deck.pptx");
slide.PageRanges = "3";
page.Add(slide);

doc.Save("slide.pdf");
doc.Close();
```

```vbnet
Dim doc As New PdfDocument()
Dim page As PdfPage = doc.AddPage()

Dim slide As New PdfPowerPointElement(50, 100, 400, 225, "deck.pptx")
slide.PageRanges = "3"
page.Add(slide)

doc.Save("slide.pdf")
doc.Close()
```

### On a template

Added to a header, footer or stamp template (`PdfDocument.Templates`), the element draws the first rendered page on every page the template applies to and re-creates its web links on each of them.

### Tagged documents

On a page of a tagged document the region carries its own structure: the slides' titles, text, tables, pictures and links join the host document's logical structure, in the reading order, at the place the region was drawn - so a document declaring PDF/UA-1 or PDF/UA-2 stays conformant with a PowerPoint region on it. `PdfPowerPointElement.Tagged` asks for that structure on a document that is not tagged itself. Set `PdfPageElement.Artifact` to declare the whole region decorative instead.

In a header, footer or stamp the element is refused on a tagged document unless it is decorative: a band repeats on every page, so its content has no single place in a reading order.

## PowerPoint sections

`PdfPowerPointSection` puts a slide of a PowerPoint presentation into the header or footer band of a converted document, the way `PdfHtmlSection` puts html there - a banner designed in PowerPoint, for example. The presentation is given as a file path or a stream (pptx, pptm, potx, potm, ppsx or ppsm). It is rendered once, and the first slide is drawn on every page that shows the band, scaled to the band's width keeping the slide's shape, and cut at the band's height. Its web links stay clickable on every page.

`PdfPowerPointSection.PageRanges` picks another slide (for example `"2"`), and `PdfPowerPointSection.Publish` a notes or handout page instead of a slide.

A section is independent of the converter it heads: a PowerPoint band can head the pages of a `PowerPointToPdf` conversion or of an `HtmlToPdf`, Word or Excel one.

> **Note:** `PdfPowerPointSection` is available only in the full commercial SelectPdf library.

### Sample Code

A banner slide as the header of an html conversion. A 16:9 slide 160 points wide is 90 points high, so the band is made that high.

```csharp
HtmlToPdf converter = new HtmlToPdf();

converter.Options.DisplayHeader = true;
converter.Header.Height = 90;
converter.Header.Add(new PdfPowerPointSection(0, 0, 160, "banner.pptx"));

PdfDocument doc = converter.ConvertUrl("https://selectpdf.com");
doc.Save("page.pdf");
doc.Close();
```

```vbnet
Dim converter As New HtmlToPdf()

converter.Options.DisplayHeader = True
converter.Header.Height = 90
converter.Header.Add(New PdfPowerPointSection(0, 0, 160, "banner.pptx"))

Dim doc As PdfDocument = converter.ConvertUrl("https://selectpdf.com")
doc.Save("page.pdf")
doc.Close()
```

The second slide of the same presentation as the footer of a Word conversion:

```csharp
WordToPdf wordConverter = new WordToPdf();

wordConverter.Options.DisplayFooter = true;
wordConverter.Footer.Height = 60;
PdfPowerPointSection footer = new PdfPowerPointSection("banner.pptx");
footer.PageRanges = "2";
wordConverter.Footer.Add(footer);

PdfDocument report = wordConverter.ConvertFile("report.docx");
report.Save("report.pdf");
report.Close();
```

```vbnet
Dim wordConverter As New WordToPdf()

wordConverter.Options.DisplayFooter = True
wordConverter.Footer.Height = 60
Dim footer As New PdfPowerPointSection("banner.pptx")
footer.PageRanges = "2"
wordConverter.Footer.Add(footer)

Dim report As PdfDocument = wordConverter.ConvertFile("report.docx")
report.Save("report.pdf")
report.Close()
```
